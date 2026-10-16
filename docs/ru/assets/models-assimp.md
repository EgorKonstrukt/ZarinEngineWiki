# Импорт моделей через Assimp

Нативный Assimp (`assimp-vc143-mt.dll` / `libassimp.so.6.0.5`) читает ноды, меши, кости и блендшейпы `aiAnimMesh` в `MeshImportData`.

## Свойства / API

| Элемент | Источник | Смысл |
|---|---|---|
| Библиотека | `importer/assimp.py` | Имя DLL/SO по ОС, `_get_dll()`, `_node_world_zup` |
| Типы | `importer/ctypes_types.py` | `aiScene/aiNode/aiMesh/aiBone/aiAnimMesh`, `MeshImportData`: `bone_weights`, `blendshape_*` |
| Скелет | `importer/anim.py` | `_collect_meshes`, `_read_bones`, `_finalize_blendshapes` |
| OBJ fast/slow | `importer/mesh.py` | `_obj_load_fast` (чанки, потоки) vs slow; `_cy_parse_obj_chunk` при Cython |
| `.import` ключи | `importer/settings.py` | `scale`, `center_pivot`, `flip_uvs`, `smooth_angle`, `gen_normals`, `gen_uvs`, `blendshapes`, `materials[]` |

## Использование в редакторе

1. Киньте модель в `assets/`; создайте `<model>.import` для scale/pivot/UV/нормалей/блендшейпов.
2. Реимпорт по изменению; скелет и блендшейпы уходят в скиннед-рендер и кривые клипа.

## Использование в коде

```python
# assets/models/orc.fbx.import
{"scale": 1.0, "center_pivot": false, "flip_uvs": true,
 "smooth_angle": 30.0, "gen_normals": true, "gen_uvs": true,
 "blendshapes": true, "materials": ["body", "eyes"]}
```

## Связанные

- Обзор ассетов, Текстуры, Клип анимации

## Проверено по

- `core/assets/importer/assimp.py`, `anim.py`, `mesh.py`, `settings.py`, `ctypes_types.py`
