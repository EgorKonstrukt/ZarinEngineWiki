# Textures

`TextureImportSettings.for_file(path)` reads `<texture>.import` JSON and applies sampler state to `moderngl.Texture`.

## Properties / API

| Key | Default | Meaning |
|---|---|---|
| `filter_mode` | trilinear | `point` (nearest, still builds mipmaps), `bilinear` (linear, no mipmaps), `trilinear` (mipmap linear) |
| `anisotropic` | 1 | `>1` sets `tex.anisotropy` |
| `max_size` | 2048 | Max dimension clamp |
| `wrap_mode` | clamp | `clamp` (`repeat_x/y` false) vs `repeat` / `mirrored_repeat` (true) |
| `compression` | none | `none/low/normal/high` (`CompressionQuality`) |
| `srgb` | true | sRGB upload flag |

Enums in code: `FilterMode{point,bilinear,trilinear}`, `WrapMode{repeat,clamp,mirrored_repeat}`, `CompressionQuality{none,low,normal,high}`.

## Usage in Editor

1. Select the texture; edit `<texture>.import` JSON keys above.
2. `point` for pixel-art, `bilinear` for UI without mipmaps, `trilinear` for 3D (default).
3. `repeat` for tiling materials, `clamp` for sprites/UI.

## Usage in Code

```python
from core.assets.texture_import_settings import TextureImportSettings

s = TextureImportSettings.for_file("assets/textures/brick.png")
print(s.filter_mode, s.wrap_mode, s.max_size)
# assets/textures/brick.png.import:
# {"filter_mode": "trilinear", "anisotropic": 4, "max_size": 2048,
#  "wrap_mode": "repeat", "compression": "none", "srgb": true}
```

## Related

- Assets Overview, Import Settings and Cache, Materials

## Verified against

- `core/assets/texture_import_settings.py`
