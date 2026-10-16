# Video, Text and SVG

> Deep guide to the three quad-based renderers: `VideoRenderer` (clip playback), `TextRenderer` (SDF atlas), and `SvgRenderer` (vector raster), with field tables and examples.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

All three render camera-facing quads collected into the frame snapshot (`core/renderer/collector/snapshot.py:258-294`) and drawn by dedicated GL backends (`videor/renderer_gl.py`, `textr/renderer.py`, `svgr/renderer.py`) using the internal `Zarin/Video`, `Zarin/Text`, and sprite-path shaders. None shows an editor gizmo icon (`_show_gizmo_icon = False` on all three).

## VideoRenderer (`core/components/rendering/renderers/video_renderer.py:11-44`)

| name | type | default | description |
|---|---|---|---|
| `video_path` | resource path | `""` | Clip; filter `Videos (*.mp4 *.avi *.mov *.mkv *.webm)`. |
| `audio_source_entity_id` | gameobject | `""` | Optional linked `AudioSource`. |
| `color` | color4 | `[1,1,1,1]` | Tint (3→4 channel auto-expand). |
| `flip_x` / `flip_y` | bool | `False` / `False` | UV flips. |
| `play_on_start` | bool | `True` | Autoplay on scene start. |
| `loop` | bool | `True` | Loop the clip. |
| `volume` | float 0–1 | `1.0` | Playback volume. |
| `offset` | float ≥ 0 (s) | `0.0` | Start offset in seconds. |

```python
vr = VideoRenderer()
vr.video_path = "assets/videos/intro.mp4"
vr.play_on_start = True
vr.loop = True
vr.volume = 0.8
entity.add_component(vr)
```

## TextRenderer (`core/components/rendering/renderers/text_renderer.py:29-112`)

| name | type | default | description |
|---|---|---|---|
| `text` | textarea | `"Text"` | String (`\r` stripped; re-parsed when `use_rich_text`). |
| `font_path` | resource path | engine default font | `Fonts (*.ttf *.otf)` via `get_default_font_path()`. |
| `font_size` | int 1–512 | `32` | Raster size. |
| `color` | color4 | `[1,1,1,1]` | Glyph color. |
| `alignment` | enum | `LEFT` | `LEFT/CENTER/RIGHT/JUSTIFY` (`TextAlign`). |
| `line_spacing` | float 0.5–5 | `1.2` | Line multiplier. |
| `bold/italic/underline/strikethrough` | bool | `False` | Style toggles. |
| `shadow` / `shadow_offset` / `shadow_color` | bool / vec2 / color | `False` / `[0.02,-0.02]` / `[0,0,0,0.5]` | Drop shadow. |
| `outline` / `outline_color` / `outline_width` | bool / color / float | `False` / black / `0.02` | SDF outline. |
| `glow` / `glow_color` / `glow_size` / `glow_intensity` | bool / color / float / float | `False` / `[0.3,0.6,1,0.5]` / `0.02` / `0.5` | SDF glow. |
| `use_3d` / `extrusion_depth` / `extrusion_layers` / `extrusion_color` | bool / float / int / color | `False` / `0.05` / `8` / grey | Extruded 3D text. |
| `atlas_resolution` | int 32–512 | `128` | Glyph atlas resolution. |
| `font_world_space` | bool | `True` | World-space sizing (`font_world_space`). |
| `billboard` | bool | `False` | Face the camera. |
| `use_rich_text` | bool | `False` | Parse rich-text tags (`rich_text_parser`). |
| `filter_mode` | enum | `LINEAR` | `NEAREST/LINEAR/TRILINEAR` (`TextFilter`). |
| `anisotropy` | float 0–16 | `4.0` | Atlas sampling anisotropy. |
| `shader` | resource path | `"text"` | Custom text shader override. |

```python
tr = TextRenderer()
tr.text = "Hello, Zarin!"
tr.font_size = 48
tr.alignment = TextAlign.CENTER
tr.outline = True
tr.atlas_resolution = 128
entity.add_component(tr)
```

## SvgRenderer (`core/components/rendering/renderers/svg_renderer.py:12-35`)

| name | type | default | description |
|---|---|---|---|
| `svg_path` | resource path | `""` | Vector file; filter `SVG (*.svg)`. |
| `color` | color4 | `[1,1,1,1]` | Tint. |
| `flip_x` | bool | `False` | Horizontal flip. |
| `flip_y` | bool | `True` | Vertical flip (SVG Y-down compensation). |
| `pixels_per_unit` | float 0.1–4096 | `100.0` | Raster scale (`ppu`); tessellation cache key is `(path, ppu)` (`svgr/tess.py:91-94`). |

Raster goes through `QSvgRenderer` (`svgr/parse.py:18-24`); per-sprite flip is uploaded as `[flip_x, flip_y]` (`svgr/renderer.py:203,290`).

```python
sr = SvgRenderer()
sr.svg_path = "assets/vectors/logo.svg"
sr.pixels_per_unit = 100.0
sr.flip_y = True
entity.add_component(sr)
```

## Related pages

- [SpriteRenderer](../sprite-renderer.md), [SvgRenderer](../svg-renderer.md), [TextRenderer](../text-renderer.md), [VideoRenderer](../video-renderer.md), [Shader Catalog](./shader-catalog.md)

## Verified against

- `core/components/rendering/renderers/video_renderer.py:11-89` (fields, defaults, serialization)
- `core/components/rendering/renderers/text_renderer.py:29-112` (fields, `atlas_resolution = 128`, `anisotropy = 4.0`, `shader = "text"`, `font_world_space = True`)
- `core/components/rendering/renderers/svg_renderer.py:12-72` (`flip_y = True`, `pixels_per_unit = 100.0`)
- `core/shaders/internal/Video.shader:7-15`, `core/shaders/internal/Text.shader:7-15`
- `core/renderer/collector/snapshot.py:258-294`, `core/renderer/videor/renderer_gl.py`, `core/renderer/svgr/parse.py:18-24`, `core/renderer/svgr/tess.py:91-94`
