# Model Import via Assimp

Native Assimp (`assimp-vc143-mt.dll` / `libassimp.so.6.0.5`) reads nodes, meshes, bones and `aiAnimMesh` blendshapes into `MeshImportData`.

## Properties / API

| Item | Source | Meaning |
|---|---|---|
| Library | `core/assets/importer/assimp.py` | `_ASSIMP_LIB_NAME` per OS, `_get_dll()`, node/world `_node_world_zup` |
| Types | `core/assets/importer/ctypes_types.py` | `aiScene/aiNode/aiMesh/aiMeshFull/aiBone/aiAnimMesh/aiFace`, `MeshImportData{arzt}`: `bone_weights`, `blendshape_names/indices/positions/normals` |
| Skeleton | `core/assets/importer/anim.py` | `_collect_meshes`, `_read_bones`, `_align_skeleton_to_mesh`, `_finalize_blendshapes` |
| OBJ fast/slow | `core/assets/importer/mesh.py`, `ctypes_types.py` | `_obj_load_fast` (chunked, threads) vs `_obj_load_slow`; `_cy_parse_obj_chunk` when Cython present |
| Materials | `core/assets/importer/material.py` | `_read_material_name`, GIF flipbook helpers |
| `.import` keys | `core/assets/importer/settings.py` | `scale`, `center_pivot`, `flip_uvs`, `smooth_angle`, `gen_normals`, `gen_uvs`, `blendshapes`, `materials[]` |

## Usage in Editor

1. Drop the model file into `assets/`; create `<model>.import` JSON to override scale/pivot/UV/normals/blendshapes.
2. Reimport on change; skeleton and blendshape arrays flow to the skinned renderer and `AnimationClip` bone curves.

## Usage in Code

```python
# assets/models/orc.fbx.import
{"scale": 1.0, "center_pivot": false, "flip_uvs": true,
 "smooth_angle": 30.0, "gen_normals": true, "gen_uvs": true,
 "blendshapes": true, "materials": ["body", "eyes"]}
```

## Related

- Assets Overview, Textures, Animation Clip, Skinned renderer

## Verified against

- `core/assets/importer/assimp.py`, `anim.py`, `mesh.py`, `settings.py`, `ctypes_types.py`, `material.py`
