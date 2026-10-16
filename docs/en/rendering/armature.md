# Armature
> Skeleton: `_ArmatureBase` + `Bone` (`core/components/rendering/skeleton/armp/bone.py`); `Armature` drives `SkinnedMeshRenderer` bone matrices.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`setup(skeleton)` copies `bone_names / bone_parents / bone_offset_matrices / bone_bind_local` (`bone.py:85-96`). `create_bone_entities(scene, root)` spawns one entity per bone with `Transform` from bind-local decompose + `Bone` (`bone.py:109-138`). Inspector shows only `bone_count` (derived) and `root_bone_name`; the arrays serialize fully (`bone.py:144-154`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `Bone.bone_name` | string | `""` | Bone name (`bone.py:25,30`). |
| `Bone.bone_index` | int | `-1` | Index into armature arrays (`bone.py:26,31`). |
| `Armature.bone_count` | int (read-only) | `len(bone_names)` | `bone.py:141-142`. |
| `Armature.root_bone_name` | string | `""` | First parent-less bone from `setup` (`bone.py:62,72,100`). |
| `Armature.bone_names` | list[string] (code) | `[]` | Serialized skeleton (`bone.py:67,147`). |
| `Armature.bone_parents` | list[int] (code) | `[]` | Parent indices, `-1` = root (`bone.py:68,148`). |
## Usage in Editor
Imported automatically with skinned models; Root Bone is informational. Do not hand-edit the arrays — reimport the model instead.
## Usage in Code
```python
arm.setup(skeleton_dict)  # bone_names, bone_parents, bone_offset_matrices, bone_bind_local
arm.create_bone_entities(scene, root_entity)
```
## Related pages
- [Skinned Mesh Renderer](./skinned-mesh-renderer.md), [Blend Shapes](./blend-shapes.md)
## Verified against
- `core/components/rendering/skeleton/armp/bone.py:14-168`
