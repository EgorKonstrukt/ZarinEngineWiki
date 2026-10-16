# Box Collider 2D

Rectangle shape in plane: platforms, walls, crates, hitboxes and trigger zones.

## Properties

| Field | Default | Notes |
|---|---|---|
| `layer` / `mask` | `0` / `0xFFFF` | Filtering |
| `offset` | `(0, 0)` | `Vec2`; world center `[ox*sx, oy*sy, 0]` |
| `size` | `(1, 1)` | `Vec2`; solver size `[sx, sy, 1.0]` |
| `is_trigger` | `False` | Overlap only |
| `physic_material` | `""` | `.zphysmat` path |
| `material_friction` / `material_bounciness` | `0.6` / `0.0` | Fallbacks |

## Usage in Editor

Set `Size`/`Offset`, layer + mask, `Is Trigger` for zones.

## Usage in Code

```python
entity = scene.create_entity("Platform")
from core.components.physics2d.box_collider2d import BoxCollider2D
from core.maths.math3d import Vec2

box = BoxCollider2D()
box.size = Vec2(4.0, 0.5)
box.offset = Vec2(0.0, 0.0)
box.is_trigger = False
entity.add_component(box)  # no Rigidbody2D = static

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Fixed update notes

- Solver call: `create_rigid_body(shape_type="box", shape_params={"size": [sx, sy, 1.0], "center": [ox, oy, 0.0]})`; `dt = Engine._fixed_dt` (`0.02` default).

## Related

Rigidbody 2D, Circle Collider 2D, Box Collider (3D), Collision Layers.

## Verified against

- `core/components/physics2d/box_collider2d.py`
- `core/physics/shapep/shapes.py`
