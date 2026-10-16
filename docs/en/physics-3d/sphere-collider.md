# Sphere Collider

Perfect ball: rolling, bouncing, head hitboxes, radial triggers and proximity sensors.

## Properties

| Field | Default | Notes |
|---|---|---|
| `layer` | `0` | `0–15` |
| `mask` | `0xFFFF` | Collide-with bitmask |
| `center` | `(0, 0, 0)` | Scaled by Transform scale (`scaled_center`) |
| `radius` | `0.5` | Inspector `0.001–10000`; world radius = `radius * max(scale)` (`scaled_radius`) |
| `is_trigger` | `False` | Overlap only |
| `physic_material` | `""` | `.zphysmat` path, wins when set |
| `material_friction` | `0.6` | Fallback |
| `material_bounciness` | `0.0` | Fallback |

## Usage in Editor

Set `Radius`/`Center`, layer + mask, `Is Trigger` for pickups/sensors. Multiple allowed per entity.

## Usage in Code

```python
entity = scene.create_entity("Ball")
from core.components.physics.rigidbody import Rigidbody
from core.components.physics.sphere_collider import SphereCollider
from core.maths.math3d import Vec3

rb = Rigidbody()
rb.mass = 2.0
entity.add_component(rb)
s = SphereCollider()
s.radius = 0.5
entity.add_component(s)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody)
    if rb is not None:
        rb.add_force(Vec3(0.0, 0.0, 10.0))
        rb.add_torque(Vec3(1.0, 0.0, 0.0))

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Fixed update notes

- Solver call: `create_rigid_body(shape_type="sphere", shape_params={"radius", "center"})`.
- Shape re-check every 60 steps; `dt` is `Engine._fixed_dt` (`0.02` default).

## Related

Rigidbody, Box/Capsule colliders, Physics Materials, Collision Layers.

## Verified against

- `core/components/physics/sphere_collider.py`
- `core/physics/shapep/shapes.py`
