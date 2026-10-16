# Blendshapes Guide

Per-mesh morph weights (facial expressions, muscle bulge). The `BlendShapes` component stores `shapes: [{name, weight}]`; the renderer reads `weights_vector()` aligned to the mesh's `blendshape_names`.

## Properties

| Item | Value |
|---|---|
| `shapes` | `[]` default; entries `{"name": str, "weight": float}` |
| Weight clamp | `_clamp_weight`: non-numeric/NaN/±inf → `0.0` |
| `get_weight(name_or_index)` / `set_weight(name_or_index, value)` | By name or index; `set_weight` appends unknown names, bumps `_weights_version`, returns whether anything changed |
| `reset_weights()` | Zeroes all, returns whether anything changed |
| `sync_with_mesh(mesh)` | Rebuilds `shapes` from `mesh.blendshape_names`, preserving known weights |
| `weights_vector(mesh=None)` | Weights in mesh order when a mesh is given, else in `shapes` order |
| Import | `core/assets/importer` `settings: {"blendshapes": True}` → `mesh.py` calls `_finalize_blendshapes` → `blendshape_names / indices / positions / normals` |

No `gen_blendshapes` helper exists in the codebase: the verified import path is the asset importer above plus `sync_with_mesh`.

## Usage in Code

```python
from core.components.rendering.deform.blendshapes import BlendShapes

bs = BlendShapes()
bs.set_weight("Smile", 0.8)   # appends {"name": "Smile", "weight": 0.8}
bs.set_weight(0, 0.5)          # by index
print(bs.get_weight("Smile"), bs.weight_count, bs.weight_names)
entity.add_component(bs)

# after assigning a mesh with morphs, align the list:
# bs.sync_with_mesh(mesh)
# renderer reads: bs.weights_vector(mesh)

# animate a blink in a script:
def on_update(self, dt):
    bs = self._entity.get_component(BlendShapes)
    w = (bs.get_weight("Blink") + dt * 2.0) % 1.0
    bs.set_weight("Blink", w)
```

`weight_*` fields are also exposed via `get_field_value("weight_Smile")` / `set_field_value`, and serialized as the `shapes` list.

## Related

Animation Overview, Import Settings, Models (Assimp).

## Verified against

- `core/components/rendering/deform/blendshapes.py:23-129` (`shapes`, clamp, get/set/sync/vector)
- `core/assets/importer/settings.py:76` (`"blendshapes": True`)
- `core/assets/importer/mesh.py:143-199` (`want_blend` → `_finalize_blendshapes`)
- `core/assets/importer/anim.py:319-364` (`blendshape_names/indices/positions/normals`)
