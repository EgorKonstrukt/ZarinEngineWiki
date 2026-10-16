# Арматура
> Скелет: `_ArmatureBase` + `Bone` (`core/components/rendering/skeleton/armp/bone.py`); `Armature` питает матрицы костей `SkinnedMeshRenderer`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`setup(skeleton)` копирует `bone_names / bone_parents / bone_offset_matrices / bone_bind_local` (`bone.py:85-96`). `create_bone_entities(scene, root)` спавнит по сущности на кость с `Transform` из bind-local разложения + `Bone` (`bone.py:109-138`). В инспекторе только `bone_count` (производное) и `root_bone_name`; массивы сериализуются полностью (`bone.py:144-154`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `Bone.bone_name` | string | `""` | Имя кости (`bone.py:25,30`). |
| `Bone.bone_index` | int | `-1` | Индекс в массивах (`bone.py:26,31`). |
| `Armature.bone_count` | int (read-only) | `len(bone_names)` | `bone.py:141-142`. |
| `Armature.root_bone_name` | string | `""` | Первая кость без родителя из `setup` (`bone.py:62,72,100`). |
| `Armature.bone_names` | list[string] (код) | `[]` | Сериализованный скелет (`bone.py:67,147`). |
| `Armature.bone_parents` | list[int] (код) | `[]` | Индексы родителей, `-1` = корень (`bone.py:68,148`). |
## Usage in Editor
Импортируется со скинированными моделями; Root Bone информативен. Массивы руками не править — переимпортировать модель.
## Usage in Code
```python
arm.setup(skeleton_dict)  # bone_names, bone_parents, bone_offset_matrices, bone_bind_local
arm.create_bone_entities(scene, root_entity)
```
## Related pages
- [Skinned Mesh Renderer](./skinned-mesh-renderer.md), [Blend Shapes](./blend-shapes.md)
## Verified against
- `core/components/rendering/skeleton/armp/bone.py:14-168`
