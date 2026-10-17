# Physics Scripting

Applying forces and reading velocity from gameplay code, plus the exact collision-callback contract.

## Rigidbody (3D)

| Member | Behavior |
|---|---|
| `rb.velocity: Vec3` | Read/write; writing sets `_velocity_dirty` so the solver picks it up. |
| `rb.angular_velocity: Vec3` | Same dirty-flag mechanism. |
| `rb.add_force(force: Vec3, world_space=True)` | Accumulates into `_force_accum`, consumed by the solver step. |
| `rb.add_torque(torque: Vec3)` | Accumulates into `_torque_accum`. |
| `rb.add_impulse(impulse: Vec3)` | Instant `velocity += impulse / mass`; ignored when `mass <= 0` or `is_kinematic`. |
| `rb.mass` / `rb.drag` / `rb.angular_drag` | `1.0` / `0.0` / `0.05` defaults. |
| `rb.use_gravity` / `rb.is_kinematic` | Gravity toggle; kinematic bodies ignore impulses. |
| `rb.freeze_pos_x/y/z`, `rb.freeze_rot_x/y/z` | Per-axis locks (serialized as `freeze_pos`/`freeze_rot` arrays). |

## Rigidbody2D

| Member | Behavior |
|---|---|
| `rb.velocity: Vec2` / `rb.angular_velocity: float` | Same dirty-flag write path. |
| `rb.add_force(force: Vec2)` | 2D force accumulator. |
| `rb.add_torque(torque: float)` | Scalar torque accumulator. |
| `rb.add_impulse(impulse: Vec2)` | Instant velocity kick, same mass/kinematic guard. |
| `rb.gravity_scale` (default `1.0`) | 2D-only gravity multiplier. |
| `rb.freeze_rotation: bool` | 2D rotation lock. |

## Collision callbacks (exact contract)

Define either arity; the engine measures the parameter count **once** at load (`2 if nargs >= 2 else 1`) and dispatches accordingly:

```python
def on_collision_enter(self, other_id): ...          # arity 1
def on_collision_enter(self, other_id, force): ...   # arity 2
def on_collision_stay(self, other_id): ...           # or (self, other_id, force)
def on_collision_exit(self, other_id): ...           # or (self, other_id, force)
```

`other_id` is the **entity id string** of the other body; `force` is the solver contact force float (`ev.get("force", 0.0)`). Callbacks come from the physics event layer, not the ECS update loop, and each contact fires on **both** entities' scripts. Resolve the entity only when needed — id comparison is cheaper:

```python
def on_collision_enter(self, other_id, force):
    if force > 50.0:
        Logger.warning(f"Hard hit by {other_id}: {force}")
```

## Jump + recoil example (full, runnable)

Forces in `on_fixed_update` (stable step), input polling in `on_update`:

```python
class Jumper:
    jump_impulse: float = 8.0
    move_force: float = 30.0

    def on_start(self):
        self.body = self._entity.get_component_by_name("Rigidbody")
        self.want_jump = False

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.SPACE):
            self.want_jump = True

    def on_fixed_update(self, dt):
        if self.body is None:
            return
        fx = Input.GetAxis("Horizontal") * float(self.move_force)
        if fx != 0.0:
            self.body.add_force(Vec3(fx, 0.0, 0.0))
        if self.want_jump:
            self.want_jump = False
            v = self.body.velocity
            self.body.velocity = Vec3(v.x, 0.0, v.z)  # reset fall speed
            self.body.add_impulse(Vec3(0.0, float(self.jump_impulse), 0.0))

    def on_collision_enter(self, other_id, force):
        if force > 40.0:
            # recoil: kick away from the impact, scaled by mass for free
            v = self.body.velocity if self.body is not None else Vec3.zero()
            if self.body is not None:
                self.body.velocity = Vec3(-v.x * 0.5, v.y, -v.z * 0.5)
```

Inspector fields: `jump_impulse`, `move_force` (floats). Requires a `Rigidbody` on the same entity and a defined `"Horizontal"` axis (see `input-reference.md`; undefined ⇒ `0.0`, so the body just won't strafe).

2D variant (gravity-aware hop):

```python
class Hopper2D:
    jump_impulse: float = 6.0

    def on_start(self):
        self.body = self._entity.get_component_by_name("Rigidbody2D")

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.SPACE) and self.body is not None:
            self.body.velocity = Vec2(self.body.velocity.x, 0.0)
            self.body.add_impulse(Vec2(0.0, float(self.jump_impulse)))
```

## Related

- `lifecycle.md` — `on_fixed_update` timing and collision rows.
- `transform-scripting.md` — move kinematic/non-physical objects via `Transform` instead.
- `input-reference.md` — `DefineAxis` (mandatory before `GetAxis` is useful).
- `pitfalls.md` — forces belong in `on_fixed_update`, not `on_update`.

## Verified against

- `core/components/physics/rigidbody.py:29-75` (fields, velocity dirty flag, `add_force`/`add_torque`/`add_impulse` + guards), `:76-98` (freeze serialization).
- `core/components/physics2d/rigidbody2d.py:30-81` (`gravity_scale`, scalar torque, `Vec2` API).
- `core/components/scripting/scriptc/hotreload.py:240-248` (arity detection `2 if nargs >= 2 else 1`).
- `core/physics/scene/events.py:294-336` (force extraction, both-sides dispatch, arity branch).
- `tests/smoke/test_scripting.py:228-255` (1-arg and 2-arg callback expectations).
