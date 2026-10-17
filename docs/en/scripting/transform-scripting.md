# Transform Scripting

Moving, rotating, and aiming entities from gameplay code. Import path: `from core.components.transform.tr import Transform`. Vectors: injected `Vec3` (no import needed inside behavior scripts).

## Accessors

| Member | Type / units | Notes |
|---|---|---|
| `transform.local_position` | `Vec3`, world units | Position relative to parent. Assign a `Vec3` (or list/tuple/ndarray). |
| `transform.local_rotation` | `Quat` | Local rotation quaternion. |
| `transform.local_euler_angles` | `Vec3`, **degrees** | Human-friendly rotation; converts via `Quat.from_euler`/`to_euler`. |
| `transform.local_scale` | `Vec3` | Local scale, default `(1, 1, 1)`. |
| `transform.position` | `Vec3`, world units | World position; the setter back-solves local position through the parent inverse. |
| `transform.world_matrix` | `Mat4` | Full world matrix; settable (goes through the deferred world-target path). |
| `transform.forward` | `Vec3` (normalized) | **−Z** of the world matrix. |
| `transform.right` | `Vec3` (normalized) | **+X** of the world matrix. |
| `transform.up` | `Vec3` (normalized) | **+Y** of the world matrix. |
| `transform.set_local_position_xyz(x, y, z)` | — | In-place write, no temp `Vec3`. |
| `transform.set_local_scale_xyz(x, y, z)` | — | In-place write. |

## Motion methods

| Signature | Behavior |
|---|---|
| `transform.translate(delta: Vec3, world_space=False)` | `False` (default): adds to **local** position. `True`: adds to **world** position (parent-aware). |
| `transform.rotate(euler: Vec3)` | Multiplies local rotation by a quaternion built from Euler angles **in degrees** (radians conversion happens inside). |
| `transform.look_at(target: Vec3, up=None)` | Points local −Z…​ forward (`-Z` convention) at `target` via `(target − position).normalized()` + `Quat.look_rotation`; default up is `Vec3.up()`. |

Every mutation marks the transform dirty and flags `_physics_dirty`, so colliders and render matrices follow on the next flush.

## Follower example (full, runnable)

```python
class Follower:
    damping: float = 5.0
    height: float = 2.0

    def on_start(self):
        from core.ecs import Entity  # noqa: F401  (annotation only)
        self.target = self._entity._scene.get_entity_by_name("Player")

    def on_update(self, dt):
        if self.target is None:
            return
        t = self._entity.transform
        goal = self.target.transform.position + Vec3(0.0, self.height, 0.0)
        cur = t.position
        k = min(1.0, float(self.damping) * float(dt))
        t.position = cur.lerp(goal, k)
        t.look_at(self.target.transform.position)
```

Inspector fields: `damping` (float), `height` (float). The target is resolved by name to keep the snippet dependency-free; an `Entity`-typed inspector field works too (see `inspector-fields.md`).

## Orbit-camera example (full, runnable)

Demonstrates `rotate` with **degrees**, `translate` in world space, and the −Z forward:

```python
class OrbitCamera:
    radius: float = 8.0
    angular_speed: float = 30.0  # degrees per second
    height: float = 3.0

    def on_start(self):
        self.angle = 0.0
        self.pivot = self._entity._scene.get_entity_by_name("Player")
        self.t = self._entity.transform

    def on_update(self, dt):
        self.angle += float(self.angular_speed) * float(dt)
        import math
        a = math.radians(self.angle)
        cx, cz = 0.0, 0.0
        if self.pivot is not None:
            p = self.pivot.transform.position
            cx, cz = p.x, p.z
        self.t.position = Vec3(
            cx + self.radius * math.cos(a),
            self.height,
            cz + self.radius * math.sin(a),
        )
        look = self.pivot.transform.position if self.pivot is not None else Vec3(0.0, 0.0, 0.0)
        self.t.look_at(look)
        # self.t.forward is now (look - position).normalized()
```

Keyboard yaw variant showing `rotate` units explicitly:

```python
class YawControl:
    turn_speed: float = 90.0  # degrees per second

    def on_update(self, dt):
        t = self._entity.transform
        if Input.GetKey(KeyCode.LEFT):
            t.rotate(Vec3(0.0, self.turn_speed * dt, 0.0))   # degrees!
        if Input.GetKey(KeyCode.RIGHT):
            t.rotate(Vec3(0.0, -self.turn_speed * dt, 0.0))
        if Input.GetKey(KeyCode.W):
            t.translate(t.forward * 5.0 * dt, world_space=True)
```

## Related

- `entity-api.md` — `entity.transform` may be `None` on bare entities.
- `input-reference.md` — key codes used above.
- `physics-scripting.md` — move kinematic bodies via velocity, dynamic ones via forces.
- `pitfalls.md` — `Vec3` has no `.rotate`; angles are degrees, not radians.

## Verified against

- `core/components/transform/tr/component.py:47-118` (local accessors, `local_euler_angles` degrees).
- `core/components/transform/tr/matrix.py:227-279` (`position`, `world_matrix`), `:281-318` (`forward −Z`, `right`, `up`), `:319-354` (`translate`), `:355-384` (`rotate`, `math.radians` inside), `:385-390` (`look_at`).
- `core/components/transform/tr/__init__.py` (class `Transform`, import path).
