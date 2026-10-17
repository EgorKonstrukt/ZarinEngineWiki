# Scripting Pitfalls

Symptom → cause → fix. Every item is grounded in the implementation cited at the end.

## 1. `Vec3(...).rotate(...)` → `AttributeError`

Symptom: rotating a vector crashes. Cause: `Vec3` has `cross/dot/length/normalized/lerp/...` but **no `rotate`**. Fix: rotate the transform, or rotate the vector with a quaternion:

```python
# wrong: v.rotate(...)
# right (object turns, degrees):
self._entity.transform.rotate(Vec3(0.0, 90.0 * dt, 0.0))
# right (vector turns, radians via axis-angle):
from core.maths.math3d import Quat
q = Quat.from_axis_angle(Vec3.up(), 1.5708)
v2 = q.rotate_vec3(v)
```

## 2. Wrong class runs (or nothing runs)

Symptom: the engine instantiates an unexpected class from your file. Cause: discovery scans `dir(module)` — **alphabetical, not definition order** — and picks the first class with any lifecycle/collision/gizmo member or `_inspector_buttons`. Fix: one behavior class per file, or name the behavior so it sorts first; helper classes must not define `on_update`/`start`/etc.

## 3. `GetAxis("Horizontal")` is always `0.0`

Symptom: axis input dead while keys work. Cause: undefined axes read as `0.0` by contract. Fix: define before use (or ship project bindings):

```python
Input.DefineAxis("Horizontal", [KeyCode.D], [KeyCode.A])
Input.DefineAxis("Vertical", [KeyCode.W], [KeyCode.S])
```

## 4. Sound starts although `play_on_awake` is off

Symptom: enabling an entity starts its clip. Cause: `AudioSource.on_enable` plays whenever `clip_path` is set and idle — independent of `play_on_awake`. Fix: clear `clip_path` until you mean it, or `stop()` in `on_enable` for ambience managers:

```python
def on_enable(self):
    src = self._entity.get_component_by_name("AudioSource")
    if src is not None and not self.want_sound:
        src.stop()
```

## 5. Cursor won't lock / mouse axis dead

Symptom: `cursorLocked` set but nothing changes, or `GetAxis("Mouse X")` is `0.0`. Cause: the flags are plain engine-bridged booleans (set both lock **and** visibility), and mouse axes need `DefineMouseAxis`. Fix:

```python
Input.set_cursor_locked(True)
Input.set_cursor_visible(False)
Input.DefineMouseAxis("Mouse X")
```

There is no gamepad API in `core/input/inpsys` — keyboard, mouse, and abstract axes/buttons only.

## 6. Cython import fails / stale compiled code

Symptom: `import fast_math` (companion `fast_math.pyx`) fails or ignores edits. Cause: the companion builds **synchronously on first import** via the in-process pyximport hook; a missing `cython` package only warns once and the import then fails. Edits are picked up because the reloader snapshots extension mtimes and rebuilds on change. Fix: keep the `.pyx` next to the `.py`, ensure `cython` is installed, touch nothing else:

```python
# myscript.py + fast_math.pyx in the SAME folder:
import fast_math  # resolved from the script folder automatically
```

## 7. Spawned entity is invisible / has no position

Symptom: `create_entity` object can't be placed. Cause: new entities are **bare — no `Transform`**. Fix: always add one (setting `transform` on a `None` crashes):

```python
from core.components.transform.tr import Transform
e = scene.create_entity("Prop")
e.add_component(Transform())   # before touching e.transform
```

## 8. `on_fixed_update` order surprises

Symptom: two scripts race (e.g. writer vs reader). Cause: updates run through cached scene-order lists; cross-entity order is an implementation detail, not a contract. Fix: don't rely on sibling order — write in `on_fixed_update`, read with a one-frame lag or resolve lazily, and never assume who runs first.

## 9. Inspector edits vanish / types wrong

Symptom: field shows but value resets, or a `Vec3` arrives as a list. Cause: two stores — `_field_values` (source of truth, serialized as `script_fields`) vs live instance attributes (re-applied every frame from `_field_values`, lists converted to `Vec2/3/4` only when the annotation matches). Fix: use `get_field_value`/`set_field_value` from tooling, keep annotations exact (`x: Vec3`, not `x = [0,0,0]`), and on disk expect `script_fields`, not `_field_values`:

```python
comp.set_field_value("speed", 7.5)
speed = comp.get_field_value("speed")
```

## 10. Angles in radians do nothing useful

Symptom: `rotate(Vec3(0, 0.05, 0))` barely turns. Cause: `Transform.rotate` takes **degrees** (radians→degrees conversion lives inside). Fix: pass degrees, convert at the boundary:

```python
t.rotate(Vec3(0.0, 45.0 * dt, 0.0))   # 45 deg/sec, not 45 rad/sec
```

## 11. `GetMouseButton(1000)` never fires

Symptom: mouse checks with `KeyCode.MOUSE_LEFT` fail. Cause: `GetMouseButton(button)` adds the **index** to `MOUSE_LEFT` — pass `0`, not `1000`. Fix: `Input.GetMouseButtonDown(0)` for left. (`Input.GetKey(KeyCode.MOUSE_LEFT)` is the code-based alternative.)

## 12. Errors name the method but not the line

Symptom: console shows `Script on_update error 'x.py': ...` with no location. Cause: `_call_py` logs only `str(e)`. Fix: develop with the Check button (`error [line N]` diagnostics) and wrap risky per-frame code with `traceback.format_exc()` logging (see `debugging.md`).

## Related

- `debugging.md` — Check formats, Logger levels, old-version fallback.
- `input-reference.md` — the full code table behind items 3, 5, 11.
- `templates.md` — fixed versions of the patterns people usually get wrong.

## Verified against

- `Vec3` surface (no `rotate`; has `lerp/to_list/zero/one/up/right/forward`) + `Quat.from_axis_angle/rotate_vec3/look_rotation`.
- `core/components/scripting/scriptc/hotreload.py:202-207` (`dir(mod)` scan), `:253-260` (`str(e)` logging), `:128-133` (`DefineAxis` passthrough).
- `core/input/inpsys/gamepad.py:45-61` (`MOUSE_LEFT + button`), `:99-126` (`0.0` for unknown axes), `:128-136` (define calls).
- `core/components/audio/source/component.py:78-84` (auto-start), `core/ecs/scene.py:229-239` (bare entity), `core/components/transform/tr/matrix.py:355-364` (degrees).
- `core/components/scripting/cythonp/loader.py:56-103` (sync pyximport build), `:189-242` (prepare/snapshot/changed), `core/components/scripting/scriptc/component.py:132-150` (`script_fields`), `fields.py:170-214` (field stores + Vec conversion).
