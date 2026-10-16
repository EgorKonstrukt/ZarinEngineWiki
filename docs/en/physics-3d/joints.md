# Joints

Connect two bodies with a constraint: doors, wheels, suspension, ragdoll limbs, breakable attachments.

## Properties

| Field | Default | Notes |
|---|---|---|
| `connected_entity_name` | `""` | Other entity by **name** (resolved in `_create_entity_joints`) |
| `anchor` | `(0, 0, 0)` | World-space anchor |
| `axis` | `(0, 0, 1)` | Hinge/slider axis |
| `joint_type` | `"hinge"` | Component enum: `hinge` / `fixed` / `spring` |
| `limit_low` / `limit_high` | `-3.14159` / `3.14159` | Radians; Inspector clamps low `≤0`, high `≥0` |
| `stiffness` / `damping` | `10.0` / `1.0` | Spring mounts |

## Usage in Editor

Add `Rigidbody` + collider to both entities, add `Joint` to one side, type the other entity's **name** into `Connected Entity`, set anchor/axis/limits.

## Usage in Code

```python
entity = scene.create_entity("Door")
from core.components.physics.joint import Joint
from core.maths.math3d import Vec3

j = Joint()
j.connected_entity_name = "Frame"
j.joint_type = "hinge"
j.anchor = Vec3(0.0, 1.0, 0.0)
j.axis = Vec3(0.0, 0.0, 1.0)
j.limit_low = -1.57
j.limit_high = 1.57
entity.add_component(j)

def on_collision_enter(self, other_id, force=0.0):
    pass
```

Solver mapping (verified): `fixed` → fixed, `point`/`point2point` → point, `hinge`/`revolute` → hinge, `slider`/`prismatic` → slider, `distance`/`spring` → distance, `cone`/`spherical` → cone. Helpers: `create_drag_constraint` (point2point + `change_constraint`), `create_fixed_constraint`, `create_hinge_constraint`, `remove_joint_constraint`.

## Fixed update notes

- Joints are created at scene load from `Joint` components; missing name or body logs a warning and skips creation.
- `dt` is `Engine._fixed_dt` (`0.02` default); joint solving runs inside `solver.step_simulation(dt)`.

## Related

Rigidbody, Motor, Collision Layers (connected bodies must share a colliding pair), Solver & Threading, Constraints (Transform followers — a separate system, not solver joints).

## Verified against

- `core/components/physics/joint.py`
- `core/physics/scene/joints.py`
- `core/physics/physics_solver.py` (`create_joint`, `change_constraint`)
- `physics_solvers/culverin_solver/solverp/broadphase.py` (`_JOINT_TYPE_MAP`)
