# PhysBoneCollider

Deflectors that keep PhysBone chains out of bodies: hair out of heads, capes out of legs, tails out of walls. CPU-only, like PhysBone itself.

## Properties

| Field | Default | Notes |
|---|---|---|
| `shape_type` | `sphere` | `sphere` / `capsule` / `plane` |
| `radius` | `0.05` | `0.001–10`; world = `radius * max(scale)`, min `0.001` |
| `height` | `0.2` | `0.001–10`; capsule full height along `direction` |
| `center` | `(0, 0, 0)` | Rotated by world quat, added to Transform position |
| `direction` | `y` | `x` / `y` / `z` capsule axis |
| `plane_normal` | `(0, 1, 0)` | Rotated into world for planes |
| `is_inside` | `False` | `True` = contain particles instead of pushing out |
| `show_gizmo` | `True` | Gizmo + `gizmo_lines` (planes only) |

## Usage in Editor

Add `PhysBoneCollider`, pick shape, size it, reference its entity from `PhysBone.collider_entity_ids`. Use `is_inside` for containment zones.

## Usage in Code

```python
entity = scene.create_entity("HeadBlocker")
from core.components.physics.pbcollider.collider import PhysBoneCollider
from core.maths.math3d import Vec3

c = PhysBoneCollider()
c.radius = 0.15
c.center = Vec3(0.0, 0.1, 0.0)
entity.add_component(c)

# pushed out of the sphere (particle radius accounted):
# pos = c.collide(pos, particle_radius)
```

`collide(pos, particle_radius)`: sphere/capsule project to `radius + pr` surface (or clamp inside when `is_inside`); planes push along `±normal` by penetration depth.

## Fixed update notes

- No solver body, no `dt`: pure positional projection sampled during the PhysBone pass each frame.

## Related

PhysBone, Sphere/Capsule colliders (rigid equivalents).

## Verified against

- `core/components/physics/pbcollider/collider.py` (`collide`)
- `core/components/physics/pbcollider/base.py`
