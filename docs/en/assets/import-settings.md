# Import Settings and Cache

Sidecar `<file>.import` JSON overrides per-asset import; mtime/stat signatures decide when to reimport.

## Properties / API

| Asset | Sidecar keys | Source |
|---|---|---|
| Mesh | `scale`, `center_pivot`, `flip_uvs`, `smooth_angle`, `gen_normals`, `gen_uvs`, `blendshapes`, `materials[]` | `core/assets/importer/settings.py` |
| Texture | `filter_mode`, `anisotropic`, `max_size`, `wrap_mode`, `compression`, `srgb` | `core/assets/texture_import_settings.py` |
| Audio | `quality` (0–100) | `core/audio/sys/clip.py` |
| Cache | stat `(mtime_ns, size)` / sha1-16 / LRU 12 / TTL 2.0 s | `importer/settings.py`, `texture_import_settings.py` |

## Usage in Editor

1. Create `<asset>.import` next to the asset with only the keys you override.
2. Touch the file to reimport; delete it to restore defaults.
3. Audio `quality: 100` keeps full rate; lower values linear-resample (`_resample`, min 8000 Hz).

## Usage in Code

```python
# mesh
{"scale": 0.01, "flip_uvs": true, "smooth_angle": 30.0}
# texture
{"filter_mode": "bilinear", "wrap_mode": "clamp", "max_size": 1024}
# audio
{"quality": 80}
```

## Related

- Assets Overview, Model Import, Textures, AudioClip

## Verified against

- `core/assets/importer/settings.py`, `core/assets/texture_import_settings.py`, `core/audio/sys/clip.py`
