# Mesh Filter
> Geometry reference: `MeshFilter` (`core/components/rendering/renderers/mesh_filter.py`) holding the model path/name; `MeshRenderer` draws it.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Pair with [Mesh Renderer](./mesh-renderer.md) on the same entity: filter selects the mesh, renderer selects materials. Mesh data itself is cached in `self._mesh_data`. Supported picker filter: `Models (*.obj *.fbx)`.
## Properties
| name | type | default | description |
|---|---|---|---|
| `mesh_name` | string | `""` | Sub-mesh / asset name (`mesh_filter.py:22,29`). |
| `mesh_path` | resource path (obj/fbx) | `""` | Source model file (`mesh_filter.py:23,28`). |
## Usage in Editor
Add Component → Mesh Filter, set Source to an `.obj`/`.fbx`, then add Mesh Renderer and assign a material per sub-mesh.
## Usage in Code
```python
mf = entity.get_component(MeshFilter)
mf.mesh_path = "assets/models/crate.obj"
mf.mesh_name = "Crate"
```
## Related pages
- [Mesh Renderer](./mesh-renderer.md), [Skinned Mesh Renderer](./skinned-mesh-renderer.md), [Materials](./materials.md)
## Verified against
- `core/components/rendering/renderers/mesh_filter.py:18-41`
