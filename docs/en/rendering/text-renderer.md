# Text Renderer
> World/UI text: `TextRenderer` (`core/components/rendering/renderers/text_renderer.py`) with SDF atlas in `core/renderer/textr/`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Enums: `TextAlign left/center/right/justify`, `TextFilter nearest/linear/trilinear` (`text_renderer.py:15-25`). Rich text (`<b>/<i>/<color>` etc. via `rich_text_parser`) re-parses on text/color/style change. `font_world_space = True` draws in world units; `billboard` faces the camera.
## Properties
| name | type | default | description |
|---|---|---|---|
| `text` | textarea | `"Text"` | Content (`text_renderer.py:39,81`). |
| `font_path` | resource path (ttf/otf) | engine default font | `get_default_font_path()` (`text_renderer.py:40,82`). |
| `font_size` | int (1–512) | `32` | Raster size (`text_renderer.py:41,83`). |
| `color` | color4 | `[1, 1, 1, 1]` | Base color (`text_renderer.py:42,84`). |
| `alignment` | `TextAlign` | `LEFT` | Line alignment (`text_renderer.py:43,85`). |
| `line_spacing` | float (0.5–5) | `1.2` | Line multiplier (`text_renderer.py:44,86`). |
| `bold` / `italic` / `underline` / `strikethrough` | bool | `False` | Style flags (`text_renderer.py:46-49`). |
| `shadow` | bool | `False` | Drop shadow toggle (`text_renderer.py:51,91`). |
| `shadow_offset` | vec2 | `[0.02, -0.02]` | Shadow offset (`text_renderer.py:52,92`). |
| `shadow_color` | color4 | `[0, 0, 0, 0.5]` | Shadow color (`text_renderer.py:53,93`). |
| `outline` | bool | `False` | Outline toggle (`text_renderer.py:55,94`). |
| `outline_color` | color4 | `[0, 0, 0, 1]` | Outline color (`text_renderer.py:56,95`). |
| `outline_width` | float (0.001–0.1) | `0.02` | Outline width (`text_renderer.py:57,96`). |
| `glow` | bool | `False` | Glow toggle (`text_renderer.py:59,97`). |
| `glow_color` | color4 | `[0.3, 0.6, 1.0, 0.5]` | Glow color (`text_renderer.py:60,98`). |
| `glow_size` | float (0.001–0.1) | `0.02` | Glow radius (`text_renderer.py:61,99`). |
| `glow_intensity` | float (0–1) | `0.5` | Glow strength (`text_renderer.py:62,100`). |
| `use_3d` | bool | `False` | Extruded 3D text (`text_renderer.py:64,101`). |
| `extrusion_depth` | float (0.001–1) | `0.05` | Depth (`text_renderer.py:65,102`). |
| `extrusion_layers` | int (1–32) | `8` | Layers (`text_renderer.py:66,103`). |
| `extrusion_color` | color3 | `[0.3, 0.3, 0.3]` | Side color (`text_renderer.py:67,104`). |
| `atlas_resolution` | int (32–512) | `128` | Glyph atlas px (`text_renderer.py:69,105`). |
| `font_world_space` | bool | `True` | World vs screen sizing (`text_renderer.py:70,106`). |
| `billboard` | bool | `False` | Face camera (`text_renderer.py:71,107`). |
| `use_rich_text` | bool | `False` | Parse rich tags (`text_renderer.py:72,111`). |
| `filter_mode` | `TextFilter` | `LINEAR` | Atlas filtering (`text_renderer.py:73,108`). |
| `anisotropy` | float (0–16) | `4.0` | Aniso filtering (`text_renderer.py:74,109`). |
| `shader` | resource path (*.shader) | `"text"` | Text shader (`text_renderer.py:76,110`). |
## Usage in Editor
Set Font + Size, Alignment, then Style/Shadow/Outline/Glow toggles. Enable Rich Text for inline tags; enable Billboard for nameplates.
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
