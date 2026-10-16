# Mesh Renderer
> Material + flags for a `MeshFilter` mesh: `MeshRenderer` (`core/components/rendering/renderers/mesh_renderer.py`).
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`materials[i].path` maps to sub-mesh `i`; `get_material_path(i)` falls back to the last entry (`mesh_renderer.py:47-52`). UV transform (`uv_scale/uv_offset`, optional `uv_scale_by_transform`) is pushed to `u_uv_scale/u_uv_offset` uniforms. `dynamic_reflections` opts into the cubemap pass.
## Properties
| name | type | default | description |
|---|---|---|---|
| `materials` | list of `{path: *.mat}` | `[{"path": "assets/materials/ProBuilderPrototype.mat"}]` | Per-submesh materials (`mesh_renderer.py:22-24,38`). |
| `sprite_texture` | resource path (png/jpg/jpeg/bmp/tga) | `""` | Extra sprite texture slot (`mesh_renderer.py:25,39`). |
| `uv_scale` | vec2 | `[1, 1]` | UV tiling (`mesh_renderer.py:27,40`). |
| `uv_offset` | vec2 | `[0, 0]` | UV offset (`mesh_renderer.py:28,41`). |
| `uv_scale_by_transform` | bool | `False` | Derive UV scale from transform (`mesh_renderer.py:29,42`). |
| `cast_shadows` | bool | `True` | Write to shadow maps (`mesh_renderer.py:31,43`). |
| `receive_shadows` | bool | `True` | Sample shadow maps (`mesh_renderer.py:32,44`). |
| `dynamic_reflections` | bool | `False` | Cubemap reflection capture (`mesh_renderer.py:33,45`). |
## Usage in Editor
Material list length should match sub-mesh count. Uncheck Cast Shadows for glass/decal helpers; uncheck Receive for emissive props.
## Usage in Code
```python
mr = entity.get_component(MeshRenderer)
mr.materials = [{"path": "assets/materials/Brick.mat"}]
mr.uv_scale = Vec2(2.0, 2.0)
mr.cast_shadows = True
```
## Material/Shader snippet
`.mat` (see [Materials](./materials.md)):
```json
{"name": "Brick", "shader_path": "default", "properties": {"_BaseColor": [1, 1, 1, 1]}}
```
## Related pages
- [Mesh Filter](./mesh-filter.md), [Materials](./materials.md), [Shaders](./shaders.md), [Shadows](./shadows.md)
## Verified against
- `core/components/rendering/renderers/mesh_renderer.py:18-83`
