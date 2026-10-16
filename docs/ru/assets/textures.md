# Текстуры и сжатие

`TextureImportSettings.for_file(path)` читает `<texture>.import` JSON и ставит сэмплер `moderngl.Texture`.

## Свойства / API

| Ключ | Умолчание | Смысл |
|---|---|---|
| `filter_mode` | trilinear | `point` (nearest), `bilinear` (linear без мипов), `trilinear` (мипы) |
| `anisotropic` | 1 | `>1` ставит `tex.anisotropy` |
| `max_size` | 2048 | Кламп стороны |
| `wrap_mode` | clamp | `clamp` vs `repeat` / `mirrored_repeat` |
| `compression` | none | `none/low/normal/high` |
| `srgb` | true | Флаг sRGB-загрузки |

## Использование в редакторе

1. Правьте `<texture>.import`; `point` — пиксель-арт, `bilinear` — UI без мипов, `trilinear` — 3D.
2. `repeat` для тайлинга, `clamp` для спрайтов/UI.

## Использование в коде

```python
from core.assets.texture_import_settings import TextureImportSettings

s = TextureImportSettings.for_file("assets/textures/brick.png")
# {"filter_mode": "trilinear", "anisotropic": 4, "max_size": 2048,
#  "wrap_mode": "repeat", "compression": "none", "srgb": true}
```

## Связанные

- Обзор ассетов, Настройки импорта, Материалы

## Проверено по

- `core/assets/texture_import_settings.py`
