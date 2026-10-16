# Terrain Collider

Heightfield collision following terrain data — smooth height sampling instead of millions of triangles.

## Properties

| Field | Default | Notes |
|---|---|---|
| `layer` / `mask` | `0` / `0xFFFF` | Filtering |
| `size` | `(1000.0, 60.0, 1000.0)` | World size `(x, y, z)` |
| `height_scale` | `60.0` | Vertical scale |
| `resolution` | `256` | Samples per side; `set_height_data` resets it from array shape |
| `center` | `(0, 0, 0)` | Local offset |
| `is_trigger` | `False` | Overlap only |
| `physic_material` | `""` | `.zphysmat` path |
| `material_friction` | `0.9` | Fallback (higher than other colliders) |
| `material_bounciness` | `0.0` | Fallback |
| `height_data` | `None` | `np.ndarray` 2D heights; set via `set_height_data` / `height_data` setter |

## Usage in Editor

Set `World Size`, `Height Scale`, `Resolution`, feed heights from the terrain system (`set_height_data`), pick layer/mask.

## Usage in Code

```python
entity = scene.create_entity("Ground")
from core.components.physics.terrain_collider import TerrainCollider
import numpy as np

tc = TerrainCollider()
tc.size = Vec3(1000.0, 60.0, 1000.0)
tc.height_scale = 60.0
tc.set_height_data(np.zeros((256, 256), dtype=np.float32))
entity.add_component(tc)  # static: no Rigidbody

def on_collision_enter(self, other_id, force=0.0):
    pass
```

`cell_size()` returns `(size.x/(res-1), size.z/(res-1))`.

## Fixed update notes

- Solver call: `create_rigid_body(shape_type="heightfield", shape_params={"size", "resolution", "height_scale", "center", "height_data"})`; Culverin decimates grids above `8000` vertices and warns when height data is missing.
- Extra-body rule: `heightfield` extras are always static bodies.
- `dt` is `Engine._fixed_dt` (`0.02` default).

## Related

Rigidbody, Mesh Collider, Physics Materials, Collision Layers.

## Verified against

- `core/components/physics/terrain_collider.py`
- `core/physics/shapep/shapes.py` (heightfield params)
- `physics_solvers/culverin_solver/solverp/solver.py` (`_create_heightfield_collider`)
