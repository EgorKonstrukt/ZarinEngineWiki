# Assets Overview

Everything under the project is an asset file plus an optional sidecar `.import` JSON. Importers read the sidecar, cache by mtime/hash, and produce runtime buffers.

## Properties / API

| Item | Source | Meaning |
|---|---|---|
| Sidecar rule | `texture_import_settings.py`, `importer/settings.py`, `audio/sys/clip.py` | `<asset>.import` JSON next to the file |
| Mesh settings | `core/assets/importer/settings.py:_read_mesh_import` | `scale` 1.0, `center_pivot` false, `flip_uvs` true, `smooth_angle` 30.0, `gen_normals`/`gen_uvs`/`blendshapes` true, `materials[]` |
| Texture settings | `core/assets/texture_import_settings.py` | `filter_mode` trilinear, `anisotropic` 1, `max_size` 2048, `wrap_mode` clamp, `compression` none, `srgb` true |
| Audio sidecar | `core/audio/sys/clip.py:load_from_file` | `quality` 0–100 resample percent |
| Cache | `importer/settings.py`, `texture_import_settings.py` | stat-signature + LRU (`_MEM_CACHE_MAX` 12, texture mtime TTL 2.0 s) |

## Usage in Editor

1. Place the file in `assets/`; a `.import` JSON appears beside it.
2. Edit the JSON keys for that type (mesh/texture/audio); reimport happens on mtime change.
3. Missing sidecar means defaults above — safe to delete to reset.

## Usage in Code

```python
# mesh .import JSON
{"scale": 1.0, "center_pivot": false, "flip_uvs": true,
 "smooth_angle": 30.0, "gen_normals": true, "gen_uvs": true, "blendshapes": true}
# texture .import JSON
{"filter_mode": "trilinear", "anisotropic": 1, "max_size": 2048,
 "wrap_mode": "clamp", "compression": "none", "srgb": true}
# audio .import JSON
{"quality": 100}
```

## Related

- Model Import, Textures, Import Settings and Cache

## Verified against

- `core/assets/importer/settings.py`, `core/assets/texture_import_settings.py`, `core/audio/sys/clip.py`
