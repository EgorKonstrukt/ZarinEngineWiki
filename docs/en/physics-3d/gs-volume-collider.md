# GS Volume Collider

Collision volumes derived from Gaussian-splat captures, so players walk on photogrammetry-style scenes with no mesh geometry.

## Properties

| Field | Default | Notes |
|---|---|---|
| `ply_path` | `""` | Splat filter `*.ply *.sog *.ssog` |
| `voxel_size` | `0.25` | Inspector `0.02–4.0` |
| `opacity_threshold` | `0.05` | Inspector `0–1` |
| `dilation` | `1` | Inspector `0–3` |
| `max_boxes` | `2048` | Inspector `64–16384` |
| `center` | `(0, 0, 0)` | Local offset added to every box |
| `layer` / `mask` | `0` / `0xFFFF` | Filtering |
| `is_trigger` | `False` | Overlap only |
| `physic_material` | `""` | `.zphysmat` path |
| `material_friction` / `material_bounciness` | `0.6` / `0.0` | Fallbacks |

## Usage in Editor

Assign `Splat Path`, tune `Voxel Size` / `Opacity Cutoff` / `Dilation` / `Max Boxes`. Gizmo draws up to 4000 boxes.

## Usage in Code

```python
entity = scene.create_entity("SplatGround")
from core.components.physics.gsvol.collider import GSVolumeCollider

gs = GSVolumeCollider()
gs.ply_path = "assets/capture.ply"
gs.voxel_size = 0.25
gs.max_boxes = 2048
entity.add_component(gs)  # static: no Rigidbody

def on_collision_enter(self, other_id, force=0.0):
    pass
```

`collider_shapes(transform)` expands the volume into a list of `box` shape dicts (size clamped to `≥1e-4`, center includes `center` offset and Transform scale); empty volumes contribute zero shapes. Requires the cython occupancy/merge helpers — without them `volume_boxes()` is empty.

## Fixed update notes

- Shapes enter the solver as ordinary `box` entries through `find_shapes_info` → compound path; `dt` is `Engine._fixed_dt` (`0.02` default).

## Related

Box Collider, Mesh Collider, Collision Layers, Physics Materials.

## Verified against

- `core/components/physics/gsvol/collider.py` (`collider_shapes`, `volume_boxes`)
- `core/physics/shapep/shapes.py` (`find_shapes_info` multi-shape branch)
