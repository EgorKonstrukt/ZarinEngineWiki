# Skinned Mesh Renderer
> Animated mesh: `SkinnedMeshRenderer` (`core/components/rendering/renderers/skinned_mesh_renderer.py`) with embedded mesh reference, GPU skinning in `core/renderer/gpu_skinning.py` + `skinned_pass.py`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Unlike static meshes, the mesh reference lives on this component (no `MeshFilter` needed). Bone matrices upload to `u_bone_matrices`; see [Armature](./armature.md). `update_when_offscreen` keeps skinning when culled (for shadows/reflections).
## Properties
| name | type | default | description |
|---|---|---|---|
| `mesh_name` | string | `""` | Mesh asset name (`skinned_mesh_renderer.py:24,38`). |
| `mesh_path` | resource path (obj/fbx/glb/gltf) | `""` | Source model (`skinned_mesh_renderer.py:25,37`). |
| `materials` | list of `{path: *.mat}` | `[{"path": ""}]` | Per-submesh materials (`skinned_mesh_renderer.py:27-29,39`). |
| `cast_shadows` | bool | `True` | Shadow caster (`skinned_mesh_renderer.py:30,40`). |
| `receive_shadows` | bool | `True` | Shadow receiver (`skinned_mesh_renderer.py:31,41`). |
| `update_when_offscreen` | bool | `True` | Update skinning offscreen (`skinned_mesh_renderer.py:32,42`). |
## Usage in Editor
Import an FBX/GLB with a skeleton, set Source, assign materials, add [Armature](./armature.md) + [Blend Shapes](./blend-shapes.md) as needed.
## Usage in Code
```python
smr = entity.get_component(SkinnedMeshRenderer)
smr.mesh_path = "assets/models/hero.fbx"
smr.materials = [{"path": "assets/materials/Hero.mat"}]
```
## Related pages
- [Armature](./armature.md), [Blend Shapes](./blend-shapes.md), [Mesh Renderer](./mesh-renderer.md)
## Verified against
- `core/components/rendering/renderers/skinned_mesh_renderer.py:20-80`
- `core/renderer/gpu_skinning.py`, `core/renderer/skinned_pass.py`
