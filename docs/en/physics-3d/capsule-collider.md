# Capsule Collider

Character bodies and pills: cylinder with spherical caps that never snags on floor seams like boxes do.

## Properties

| Field | Default | Notes |
|---|---|---|
| `layer` | `0` | `0–15` |
| `mask` | `0xFFFF` | Collide-with bitmask |
| `center` | `(0, 0, 0)` | `scaled_center` multiplies by Transform scale |
| `radius` | `0.5` | Inspector `0.001–10000`; world = `radius * max(scale)` |
| `height` | `2.0` | Inspector `0.001–10000`; world = `height * axis_scale` (`scaled_height`) |
| `direction` | `1` | `0` = X, `1` = Y, `2` = Z |
| `is_trigger` | `False` | Overlap only |
| `physic_material` | `""` | `.zphysmat` path |
| `material_friction` | `0.6` | Fallback |
| `material_bounciness` | `0.0` | Fallback |

## Usage in Editor

Set `Radius`/`Height`, axis via `direction` (code/serialized; Inspector exposes radius/height/trigger/material). Use Y (`1`) for upright characters.

## Usage in Code

```python
entity = scene.create_entity("Pill")
from core.components.physics.rigidbody import Rigidbody
from core.components.physics.capsule_collider import CapsuleCollider

rb = Rigidbody()
rb.mass = 80.0
rb.freeze_rot_x = True
rb.freeze_rot_z = True
entity.add_component(rb)
cap = CapsuleCollider()
cap.radius = 0.4
cap.height = 1.8
cap.direction = 1
entity.add_component(cap)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody)
    if rb is not None:
        rb.add_force(Vec3(0.0, 0.0, 50.0))

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Fixed update notes

- Solver call: `create_rigid_body(shape_type="capsule", shape_params={"radius", "height", "center", "direction"})`.
- `dt` is `Engine._fixed_dt` (`0.02` default); shape re-check every 60 steps.

## Related

Rigidbody, CharacterController, Box/Sphere colliders, Physics Materials, Collision Layers.

## Verified against

- `core/components/physics/capsule_collider.py`
- `core/physics/shapep/shapes.py`
