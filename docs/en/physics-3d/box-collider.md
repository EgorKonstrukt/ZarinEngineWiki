# Box Collider

Axis-aligned box shape in local space. The cheapest shape in any solver — crates, walls, floors, triggers, buttons.

## Properties

| Field | Default | Notes |
|---|---|---|
| `layer` | `0` | Collision layer index, `0–15` |
| `mask` | `0xFFFF` | Collide-with bitmask |
| `center` | `(0, 0, 0)` | Local offset, scaled by Transform scale (`scaled_center`) |
| `size` | `(1, 1, 1)` | Full extents, scaled by Transform scale (`scaled_size`) |
| `is_trigger` | `False` | Reports overlaps, no contact response |
| `physic_material` | `""` | `.zphysmat` asset path; wins over inline values when set |
| `material_friction` | `0.6` | Inline fallback |
| `material_bounciness` | `0.0` | Inline fallback |

## Usage in Editor

Set `Size`/`Center`, pick `Layer` + `Collision Mask`, tick `Is Trigger` for sensors. Multiple `BoxCollider` components allowed per entity (compound).

## Usage in Code

```python
entity = scene.create_entity("Crate")
from core.components.physics.rigidbody import Rigidbody
from core.components.physics.box_collider import BoxCollider
from core.maths.math3d import Vec3

rb = Rigidbody()
rb.mass = 1.0
entity.add_component(rb)
box = BoxCollider()
box.size = Vec3(1.0, 1.0, 1.0)
box.center = Vec3(0.0, 0.5, 0.0)
box.is_trigger = False
box.layer = 0
box.mask = 0xFFFF
entity.add_component(box)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody)
    if rb is not None:
        rb.add_force(Vec3(5.0, 0.0, 0.0))

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Fixed update notes

- Shape key (`size`, `center`, friction, restitution, trigger) is re-checked every 60 physics steps (`_shape_check_counter`); changed shapes rebuild the body (`rebuild_entity`).
- Solver call: `create_rigid_body(shape_type="box", shape_params={"size", "center"})`; bodies with several primitive shapes merge via `create_compound_rigid_body`.
- `dt` is `Engine._fixed_dt` (`0.02` default).

## Related

Rigidbody, Sphere/Capsule/Mesh colliders, Physics Materials, Collision Layers, Solver & Threading.

## Verified against

- `core/components/physics/box_collider.py`
- `core/physics/shapep/shapes.py` (`_shape_info_for`, `SHAPE_TYPE_MAP`)
- `core/physics/scene/bodies.py` (`_check_shape_changes`)
