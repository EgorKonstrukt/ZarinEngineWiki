# Motor

Constant drive for conveyors, wheels, fans, spinning traps, elevators and servo doors — steady motion without scripting velocity every frame.

## Properties

| Field | Default | Notes |
|---|---|---|
| `connected_entity_id` | `""` | Other entity by **id** (unlike `Joint`, which uses name) |
| `motor_type` | `hinge` | `hinge` / `slider` |
| `mode` | `position` | `position` (servo to `target`) / `velocity` (integrate `target_velocity`) |
| `axis` | `(0, 0, 1)` | Drive axis |
| `anchor` | `(0, 0, 0)` | Constraint anchor |
| `target` | `0.0` | Position target (or initial running velocity seed) |
| `target_velocity` | `0.0` | Added as `running += target_velocity * dt` in velocity mode |
| `motor_force` / `motor_torque` | `500.0` / `500.0` | Passed as `max_force`/`max_torque` |
| `limit_low` / `limit_high` | `-100000.0` / `100000.0` | Wide-open by default |
| `free_spin` | `False` | Stored flag (constraint creation does not read it; `stiffness`/`damping` are passed as `0.0`) |

## Usage in Editor

Add `Rigidbody` + collider to both bodies, add `Motor`, link `Connected Entity` (id), pick `hinge`/`slider` and `position`/`velocity`, set force/torque and limits.

## Usage in Code

```python
entity = scene.create_entity("Wheel")
from core.components.physics.motor import Motor, MotorType, MotorMode
from core.maths.math3d import Vec3

m = Motor()
m.connected_entity_id = chassis_id
m.motor_type = MotorType.HINGE
m.mode = MotorMode.VELOCITY
m.axis = Vec3(0.0, 0.0, 1.0)
m.target_velocity = 5.0
m.motor_force = 500.0
entity.add_component(m)
m.set_target(1.57)          # position mode servo
m.set_mode(MotorMode.POSITION)
```

Constraint created in `on_start`/`on_enable` via `solver.create_joint` + `enable_constraint`; removed in `on_disable`.

## Fixed update notes

- `on_fixed_update(dt)` with `dt = Engine._fixed_dt` (`0.02` default): velocity mode pushes `set_motor_target(running)` every step; position mode re-pushes `target`.
- Requires `single` mode (`_resolve_solver` → `plugin.ensure_single_mode()`), same as `CharacterController`.

## Related

Joints, Rigidbody, Solver & Threading (mode requirement), Constraints (Transform followers — a separate system, not solver joints).

## Verified against

- `core/components/physics/motor.py`
- `core/physics/physics_solver.py` (`set_motor_target`, `enable_constraint`)
