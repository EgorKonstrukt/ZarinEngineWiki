# Mesh Filter
> Ссылка на геометрию: `MeshFilter` (`core/components/rendering/renderers/mesh_filter.py`) хранит путь/имя модели; рисует её `MeshRenderer`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Пара на одной сущности с [Mesh Renderer](./mesh-renderer.md): фильтр выбирает меш, рендерер — материалы. Данные кэшируются в `self._mesh_data`. Фильтр пикера: `Models (*.obj *.fbx)`.
## Properties
| name | type | default | description |
|---|---|---|---|
| `mesh_name` | string | `""` | Имя меша/подмеша (`mesh_filter.py:22,29`). |
| `mesh_path` | путь (obj/fbx) | `""` | Файл модели (`mesh_filter.py:23,28`). |
## Usage in Editor
Add Component → Mesh Filter, Source — `.obj`/`.fbx`, затем Mesh Renderer и материал на подмеш.
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
