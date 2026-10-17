# Transforms

Transform math (`core/components/transform/tr/matrix.py:23-390`) composes TRS in row-major `float64`: each row of the rotation is scaled (`out[0,:3] = sx*R[0]` …), translation sits in row 3, and world matrices chain as `world = local @ parent_world`.

## Degrees vs radians

`Transform.rotate(euler: Vec3)` and `local_euler_angles` are **degrees**: `rotate` applies `math.radians` internally before building the delta quaternion and post-multiplies the local rotation; the getter/setter round-trip through `Quat.from_euler` / `Quat.to_euler` (both degree-based). Radian variants exist only in `math_helpers` (`quat_from_euler_rad`, `mat4_perspective(fov_rad, ...)`).

## Direction conventions

| Member | Definition |
|---|---|
| `forward` | `-`row 2 of world matrix, normalized (guard `1e-10`, else zero) → default `(0,0,-1)` |
| `right` | row 0, normalized |
| `up` | row 1, normalized |
| `look_at(target, up=None)` | `up` defaults to `Vec3.up()`; sets local rotation via `Quat.look_rotation((target-position).normalized(), up)` |

## Local vs world translation

`translate(delta, world_space=False)`: default appends to the local position; `world_space=True` adds to the world `position` property (which resolves through the parent's inverse matrix).

## Examples

```python
from core.maths.math3d import Vec3

t = entity.transform
t.translate(Vec3(0.0, 0.0, 5.0))             # local move
t.translate(Vec3(0.0, 1.0, 0.0), world_space=True)  # world-space move
t.rotate(Vec3(0.0, 45.0, 0.0))               # degrees, post-multiplied
t.look_at(Vec3(0.0, 1.0, 0.0))               # up defaults to Vec3.up()
print(t.forward.to_list())                   # -Z facing
print(t.local_euler_angles.to_list())        # degrees

# Orbit camera (frame-rate independent via dt):
import math
angle = 0.0
def on_update(dt):
    global angle
    angle += 20.0 * dt                        # 20 deg/sec
    r = 8.0
    a = math.radians(angle)
    t.position = Vec3(math.sin(a) * r, 3.0, math.cos(a) * r)
    t.look_at(Vec3(0.0, 1.0, 0.0))
```

## Related

- [Math overview](overview.md)
- [Vectors](vectors.md), [Quaternions](quaternions.md), [Matrices](matrices.md), [Coordinates](coordinates.md)
- [Curve API](../core/advanced/curve-api.md)
- [Input and Math API](../scripting/input-math-api.md)

## Verified against

- `core/components/transform/tr/matrix.py:23-72` (`_write_local_into`, TRS rows), `:281-318` (`forward`/`right`/`up`), `:319-354` (`translate`), `:355-384` (`rotate`, degrees), `:385-390` (`look_at`)
- `core/components/transform/tr/component.py:108-116` (`local_euler_angles`, degrees)
- `core/pyx/_math_vec.pyx:261-283` (`up`/`forward`/`right`)
