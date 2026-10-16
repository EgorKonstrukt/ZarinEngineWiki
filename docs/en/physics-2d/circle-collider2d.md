# Circle Collider 2D

Disc shape in plane: balls, wheels, coins, radial triggers and proximity sensors.

## Properties

| Field | Default | Notes |
|---|---|---|
| `layer` / `mask` | `0` / `0xFFFF` | Filtering |
| `offset` | `(0, 0)` | `Vec2`; world center `[ox*sx, oy*sy, 0]` |
| `radius` | `0.5` | Inspector `0.001–10000`; world = `radius * max(sx, sy)` |
| `is_trigger` | `False` | Overlap only |
| `physic_material` | `""` | `.zphysmat` path |
| `material_friction` / `material_bounciness` | `0.6` / `0.0` | Fallbacks |

## Usage in Editor

Set `Radius`/`Offset`, layer + mask, `Is Trigger` for pickups.

## Usage in Code

```python
entity = scene.create_entity("Coin")
from core.components.physics2d.rigidbody2d import Rigidbody2D
from core.components.physics2d.circle_collider2d import CircleCollider2D

c = CircleCollider2D()
c.radius = 0.3
c.is_trigger = True
entity.add_component(c)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody2D)
    if rb is not None:
        rb.add_force(Vec2(5.0, 0.0))

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Fixed update notes

- Solver call: `create_rigid_body(shape_type="sphere", shape_params={"radius", "center": [ox, oy, 0.0]})`; `dt = Engine._fixed_dt` (`0.02` default).

## Related

Rigidbody 2D, Box Collider 2D, Sphere Collider (3D), Collision Layers.

## Verified against

- `core/components/physics2d/circle_collider2d.py`
- `core/physics/shapep/shapes.py`
