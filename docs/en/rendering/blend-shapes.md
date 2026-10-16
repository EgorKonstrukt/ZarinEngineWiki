# Blend Shapes
> Morph targets: `BlendShapes` (`core/components/rendering/deform/blendshapes.py`) with `shapes[i] = {name, weight}` driving mesh `blendshape_names` order.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Weights are clamped (NaN/inf → `0.0`, `_clamp_weight`). `get_weight/set_weight` accept name or index; unknown names are appended (`blendshapes.py:56-87`). `sync_with_mesh(mesh)` rebuilds the list from `mesh.blendshape_names` preserving weights; `weights_vector(mesh)` returns GPU-order floats (`blendshapes.py:99-128`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `shapes` | list of `{name: string, weight: float 0–1}` | `[]` | Morph list (`blendshapes.py:34-37,42`). |
## Usage in Editor
Add Component → Blend Shapes on a skinned/static mesh entity, Sync with mesh, then paint weights per shape (0–1).
## Usage in Code
```python
bs = entity.get_component(BlendShapes)
bs.sync_with_mesh(mesh)
bs.set_weight("Smile", 0.75)
bs.weights_vector(mesh)
```
## Related pages
- [Skinned Mesh Renderer](./skinned-mesh-renderer.md), [Armature](./armature.md), [Mesh Renderer](./mesh-renderer.md)
## Verified against
- `core/components/rendering/deform/blendshapes.py:30-173`
