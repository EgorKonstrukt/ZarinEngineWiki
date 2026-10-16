# Rigidbody 2D

Planar dynamics for side-scrollers and top-down games — same API shape as 3D, constrained to the plane by the solver flag.

## Properties

| Field | Default | Notes |
|---|---|---|
| `mass` | `1.0` | Inspector `0.001–100000`; `0.0` when kinematic at creation |
| `drag` | `0.0` | `0–1000` |
| `angular_drag` | `0.05` | `0–1000` |
| `gravity_scale` | `1.0` | `0–100` multiplier on gravity |
| `is_kinematic` | `False` | Transform-driven |
| `freeze_rotation` | `False` | Lock spin |
| `velocity` | `Vec2.zero()` | Setter marks dirty |
| `angular_velocity` | `0.0` | Float; setter marks dirty |

## Usage in Editor

Add `Rigidbody2D` + `BoxCollider2D`/`CircleCollider2D`. Tick `Freeze Rotation` for platformer heroes.

## Usage in Code

```python
entity = scene.create_entity("Hero2D")
from core.components.physics2d.rigidbody2d import Rigidbody2D
from core.components.physics2d.box_collider2d import BoxCollider2D

rb = Rigidbody2D()
rb.mass = 1.0
rb.gravity_scale = 1.0
rb.freeze_rotation = True
entity.add_component(rb)
entity.add_component(BoxCollider2D())

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody2D)
    if rb is None or rb.is_kinematic:
        return
    rb.add_force(Vec2(0.0, 30.0))  # cleared after push; call every step
    rb.add_torque(1.0)
    rb.add_impulse(Vec2(0.0, 5.0))

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Fixed update notes

- `dt = Engine._fixed_dt` (`0.02` default); worker zeroes `vz` and `tilt` rates after `step_simulation(dt)`; kinematic/dirty bodies skipped on read-back.

## Related

Box/Circle Collider 2D, Rigidbody (3D), Collision Layers, Solver & Threading.

## Verified against

- `core/components/physics2d/rigidbody2d.py`
- `core/physics/scene/scene.py` (`_constrain_2d_bodies`)
- `core/physics/physics_worker.py` (2D velocity clamp)
