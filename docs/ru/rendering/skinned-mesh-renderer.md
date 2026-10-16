# Skinned Mesh Renderer
> Анимированный меш: `SkinnedMeshRenderer` (`core/components/rendering/renderers/skinned_mesh_renderer.py`) со встроенной ссылкой на меш, скиннинг на GPU в `core/renderer/gpu_skinning.py` + `skinned_pass.py`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
В отличие от статики, ссылка на меш — на самом компоненте (`MeshFilter` не нужен). Матрицы костей уходят в `u_bone_matrices`; см. [Armature](./armature.md). `update_when_offscreen` продолжает скиннинг вне кадра (для теней/отражений).
## Properties
| name | type | default | description |
|---|---|---|---|
| `mesh_name` | string | `""` | Имя меша (`skinned_mesh_renderer.py:24,38`). |
| `mesh_path` | путь (obj/fbx/glb/gltf) | `""` | Файл модели (`skinned_mesh_renderer.py:25,37`). |
| `materials` | list `{path: *.mat}` | `[{"path": ""}]` | Материалы подмешей (`skinned_mesh_renderer.py:27-29,39`). |
| `cast_shadows` | bool | `True` | Кастер теней (`skinned_mesh_renderer.py:30,40`). |
| `receive_shadows` | bool | `True` | Приёмник теней (`skinned_mesh_renderer.py:31,41`). |
| `update_when_offscreen` | bool | `True` | Обновлять вне кадра (`skinned_mesh_renderer.py:32,42`). |
## Usage in Editor
Импорт FBX/GLB со скелетом, Source, материалы, плюс [Armature](./armature.md) + [Blend Shapes](./blend-shapes.md) по нужде.
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
