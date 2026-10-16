# SVG-рендерер
> Векторный спрайт: `SvgRenderer` (`core/components/rendering/renderers/svg_renderer.py`), тесселяция в `core/renderer/svgr/`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Парсинг `.svg` (`svgr/parse.py`), тесселяция (`svgr/tess.py`), отрисовка `svgr/renderer.py`. Мировой размер = пиксели SVG / `pixels_per_unit`.
## Properties
| name | type | default | description |
|---|---|---|---|
| `svg_path` | путь (*.svg) | `""` | Векторный файл (`svg_renderer.py:22,31`). |
| `color` | color4 | `[1, 1, 1, 1]` | Тинт (`svg_renderer.py:23,32`). |
| `flip_x` | bool | `False` | Зеркало по горизонтали (`svg_renderer.py:24,33`). |
| `flip_y` | bool | `True` | Зеркало по вертикали под Y-down SVG (`svg_renderer.py:25,34`). |
| `pixels_per_unit` | float (0.1–4096) | `100.0` | Пикселей на единицу (`svg_renderer.py:26,35`). |
## Usage in Editor
Add Component → SVG Renderer, назначить `.svg`. Pixels/Unit выше — мельче, ниже — крупнее. Flip Y по умолчанию вкл под координаты SVG.
## Usage in Code
```python
sv = entity.get_component(SvgRenderer)
sv.svg_path = "assets/icons/logo.svg"
sv.pixels_per_unit = 200.0
```
## Related pages
- [Sprite Renderer](./sprite-renderer.md), [Text Renderer](./text-renderer.md)
## Verified against
- `core/components/rendering/renderers/svg_renderer.py:18-72`
- `core/renderer/svgr/parse.py`, `core/renderer/svgr/tess.py`, `core/renderer/svgr/renderer.py`
