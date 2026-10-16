# Mesh Renderer
> Материалы + флаги для меша `MeshFilter`: `MeshRenderer` (`core/components/rendering/renderers/mesh_renderer.py`).
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`materials[i].path` маппится на подмеш `i`; `get_material_path(i)` откатывается на последнюю запись (`mesh_renderer.py:47-52`). UV-трансформ (`uv_scale/uv_offset`, опционально `uv_scale_by_transform`) уходит в юниформы `u_uv_scale/u_uv_offset`. `dynamic_reflections` включает cubemap-проход.
## Properties
| name | type | default | description |
|---|---|---|---|
| `materials` | list `{path: *.mat}` | `[{"path": "assets/materials/ProBuilderPrototype.mat"}]` | Материалы подмешей (`mesh_renderer.py:22-24,38`). |
| `sprite_texture` | путь (png/jpg/jpeg/bmp/tga) | `""` | Доп. слот текстуры (`mesh_renderer.py:25,39`). |
| `uv_scale` | vec2 | `[1, 1]` | Тайлинг UV (`mesh_renderer.py:27,40`). |
| `uv_offset` | vec2 | `[0, 0]` | Сдвиг UV (`mesh_renderer.py:28,41`). |
| `uv_scale_by_transform` | bool | `False` | Масштаб UV из трансформа (`mesh_renderer.py:29,42`). |
| `cast_shadows` | bool | `True` | Писать в карты теней (`mesh_renderer.py:31,43`). |
| `receive_shadows` | bool | `True` | Читать карты теней (`mesh_renderer.py:32,44`). |
| `dynamic_reflections` | bool | `False` | Захват cubemap (`mesh_renderer.py:33,45`). |
## Usage in Editor
Длина списка материалов = числу подмешей. Cast Shadows снимать у стекла/декалей; Receive — у эмиссивов.
## Usage in Code
```python
mr = entity.get_component(MeshRenderer)
mr.materials = [{"path": "assets/materials/Brick.mat"}]
mr.uv_scale = Vec2(2.0, 2.0)
mr.cast_shadows = True
```
## Material/Shader snippet
`.mat` (см. [Materials](./materials.md)):
```json
{"name": "Brick", "shader_path": "default", "properties": {"_BaseColor": [1, 1, 1, 1]}}
```
## Related pages
- [Mesh Filter](./mesh-filter.md), [Materials](./materials.md), [Shaders](./shaders.md), [Shadows](./shadows.md)
## Verified against
- `core/components/rendering/renderers/mesh_renderer.py:18-83`
