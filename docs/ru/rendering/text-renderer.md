# Текст-рендерер
> Мировой/UI текст: `TextRenderer` (`core/components/rendering/renderers/text_renderer.py`) с SDF-атласом в `core/renderer/textr/`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Энамы: `TextAlign left/center/right/justify`, `TextFilter nearest/linear/trilinear` (`text_renderer.py:15-25`). Rich-текст перепарсивается при смене текста/цвета/стиля. `font_world_space = True` — размер в мировых единицах; `billboard` — лицом к камере.
## Properties
| name | type | default | description |
|---|---|---|---|
| `text` | textarea | `"Text"` | Содержимое (`text_renderer.py:39,81`). |
| `font_path` | путь (ttf/otf) | дефолтный шрифт движка | `get_default_font_path()` (`text_renderer.py:40,82`). |
| `font_size` | int (1–512) | `32` | Размер растра (`text_renderer.py:41,83`). |
| `color` | color4 | `[1, 1, 1, 1]` | Базовый цвет (`text_renderer.py:42,84`). |
| `alignment` | `TextAlign` | `LEFT` | Выравнивание (`text_renderer.py:43,85`). |
| `line_spacing` | float (0.5–5) | `1.2` | Межстрочник (`text_renderer.py:44,86`). |
| `bold` / `italic` / `underline` / `strikethrough` | bool | `False` | Стиль (`text_renderer.py:46-49`). |
| `shadow` | bool | `False` | Тумблер тени (`text_renderer.py:51,91`). |
| `shadow_offset` | vec2 | `[0.02, -0.02]` | Сдвиг тени (`text_renderer.py:52,92`). |
| `shadow_color` | color4 | `[0, 0, 0, 0.5]` | Цвет тени (`text_renderer.py:53,93`). |
| `outline` | bool | `False` | Тумблер контура (`text_renderer.py:55,94`). |
| `outline_color` | color4 | `[0, 0, 0, 1]` | Цвет контура (`text_renderer.py:56,95`). |
| `outline_width` | float (0.001–0.1) | `0.02` | Ширина контура (`text_renderer.py:57,96`). |
| `glow` | bool | `False` | Тумблер свечения (`text_renderer.py:59,97`). |
| `glow_color` | color4 | `[0.3, 0.6, 1.0, 0.5]` | Цвет свечения (`text_renderer.py:60,98`). |
| `glow_size` | float (0.001–0.1) | `0.02` | Радиус (`text_renderer.py:61,99`). |
| `glow_intensity` | float (0–1) | `0.5` | Сила (`text_renderer.py:62,100`). |
| `use_3d` | bool | `False` | Экструзия 3D (`text_renderer.py:64,101`). |
| `extrusion_depth` | float (0.001–1) | `0.05` | Глубина (`text_renderer.py:65,102`). |
| `extrusion_layers` | int (1–32) | `8` | Слои (`text_renderer.py:66,103`). |
| `extrusion_color` | color3 | `[0.3, 0.3, 0.3]` | Цвет боков (`text_renderer.py:67,104`). |
| `atlas_resolution` | int (32–512) | `128` | Атлас глифов, px (`text_renderer.py:69,105`). |
| `font_world_space` | bool | `True` | Мировой vs экранный размер (`text_renderer.py:70,106`). |
| `billboard` | bool | `False` | Лицом к камере (`text_renderer.py:71,107`). |
| `use_rich_text` | bool | `False` | Парсить rich-теги (`text_renderer.py:72,111`). |
| `filter_mode` | `TextFilter` | `LINEAR` | Фильтрация атласа (`text_renderer.py:73,108`). |
| `anisotropy` | float (0–16) | `4.0` | Анизотропия (`text_renderer.py:74,109`). |
| `shader` | путь (*.shader) | `"text"` | Шейдер текста (`text_renderer.py:76,110`). |
## Usage in Editor
Шрифт + размер, выравнивание, затем тумблеры Style/Shadow/Outline/Glow. Rich Text для инлайн-тегов; Billboard для неймплейтов.
## Usage in Code
```python
tr = entity.get_component(TextRenderer)
tr.text = "HP: 100"
tr.font_size = 48
tr.alignment = TextAlign.CENTER
tr.outline = True
tr.outline_width = 0.03
```
## Related pages
- [Sprite Renderer](./sprite-renderer.md), [SVG Renderer](./svg-renderer.md), [Materials](./materials.md)
## Verified against
- `core/components/rendering/renderers/text_renderer.py:28-112,270-348`
- `core/renderer/textr/renderer.py`, `atlas.py`, `layout.py`
