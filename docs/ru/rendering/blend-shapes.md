# Бленд-шейпы
> Морф-таргеты: `BlendShapes` (`core/components/rendering/deform/blendshapes.py`), `shapes[i] = {name, weight}` в порядке `blendshape_names` меша.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Веса клампятся (NaN/inf → `0.0`, `_clamp_weight`). `get_weight/set_weight` принимают имя или индекс; неизвестные имена добавляются (`blendshapes.py:56-87`). `sync_with_mesh(mesh)` перестраивает список из `mesh.blendshape_names` с сохранением весов; `weights_vector(mesh)` отдаёт float-ы в порядке GPU (`blendshapes.py:99-128`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `shapes` | list `{name: string, weight: float 0–1}` | `[]` | Список морфов (`blendshapes.py:34-37,42`). |
## Usage in Editor
Add Component → Blend Shapes на сущность меша, Sync with mesh, затем веса 0–1 по шейпам.
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
