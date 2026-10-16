# SVG Renderer
> Vector sprite: `SvgRenderer` (`core/components/rendering/renderers/svg_renderer.py`), tessellated by `core/renderer/svgr/`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Parses `.svg` (`svgr/parse.py`), tessellates (`svgr/tess.py`), draws via `svgr/renderer.py`. World size = SVG pixels / `pixels_per_unit`.
## Properties
| name | type | default | description |
|---|---|---|---|
| `svg_path` | resource path (*.svg) | `""` | Vector file (`svg_renderer.py:22,31`). |
| `color` | color4 | `[1, 1, 1, 1]` | Tint (`svg_renderer.py:23,32`). |
| `flip_x` | bool | `False` | Mirror horizontally (`svg_renderer.py:24,33`). |
| `flip_y` | bool | `True` | Mirror vertically for SVG Y-down (`svg_renderer.py:25,34`). |
| `pixels_per_unit` | float (0.1–4096) | `100.0` | Pixels per world unit (`svg_renderer.py:26,35`). |
## Usage in Editor
Add Component → SVG Renderer, assign an `.svg`. Raise Pixels/Unit to shrink, lower to enlarge. Flip Y defaults on to match SVG coordinates.
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
