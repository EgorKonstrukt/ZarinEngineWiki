# Видео, текст и SVG

> Углублённое руководство по трём квад-рендерерам: `VideoRenderer` (проигрывание клипов), `TextRenderer` (SDF-атлас) и `SvgRenderer` (векторный растр), с таблицами полей и примерами.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Все три рисуют квады, обращённые к камере, собранные во фрейм-снапшот (`core/renderer/collector/snapshot.py:258-294`) и отрисованные выделенными GL-бэкендами (`videor/renderer_gl.py`, `textr/renderer.py`, `svgr/renderer.py`) на внутренних шейдерах `Zarin/Video`, `Zarin/Text` и спрайт-пути. Редакторной иконки гизмо нет ни у одного (`_show_gizmo_icon = False` у всех трёх).

## VideoRenderer (`core/components/rendering/renderers/video_renderer.py:11-44`)

| name | type | default | description |
|---|---|---|---|
| `video_path` | resource path | `""` | Клип; фильтр `Videos (*.mp4 *.avi *.mov *.mkv *.webm)`. |
| `audio_source_entity_id` | gameobject | `""` | Опциональный связанный `AudioSource`. |
| `color` | color4 | `[1,1,1,1]` | Тинт (авто-расширение 3→4 канала). |
| `flip_x` / `flip_y` | bool | `False` / `False` | Флипы UV. |
| `play_on_start` | bool | `True` | Автоплей при старте сцены. |
| `loop` | bool | `True` | Зацикливание клипа. |
| `volume` | float 0–1 | `1.0` | Громкость воспроизведения. |
| `offset` | float ≥ 0 (с) | `0.0` | Стартовое смещение в секундах. |

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
| `text` | textarea | `"Text"` | Строка (`\r` вырезается; перепарс при `use_rich_text`). |
| `font_path` | resource path | дефолтный шрифт движка | `Fonts (*.ttf *.otf)` через `get_default_font_path()`. |
| `font_size` | int 1–512 | `32` | Размер растра. |
| `color` | color4 | `[1,1,1,1]` | Цвет глифов. |
| `alignment` | enum | `LEFT` | `LEFT/CENTER/RIGHT/JUSTIFY` (`TextAlign`). |
| `line_spacing` | float 0.5–5 | `1.2` | Межстрочный множитель. |
| `bold/italic/underline/strikethrough` | bool | `False` | Тогглы стиля. |
| `shadow` / `shadow_offset` / `shadow_color` | bool / vec2 / color | `False` / `[0.02,-0.02]` / `[0,0,0,0.5]` | Тень. |
| `outline` / `outline_color` / `outline_width` | bool / color / float | `False` / чёрный / `0.02` | SDF-контур. |
| `glow` / `glow_color` / `glow_size` / `glow_intensity` | bool / color / float / float | `False` / `[0.3,0.6,1,0.5]` / `0.02` / `0.5` | SDF-свечение. |
| `use_3d` / `extrusion_depth` / `extrusion_layers` / `extrusion_color` | bool / float / int / color | `False` / `0.05` / `8` / серый | Экструдированный 3D-текст. |
| `atlas_resolution` | int 32–512 | `128` | Разрешение атласа глифов. |
| `font_world_space` | bool | `True` | Размер в мировых единицах (`font_world_space`). |
| `billboard` | bool | `False` | Поворот к камере. |
| `use_rich_text` | bool | `False` | Парс rich-text тегов (`rich_text_parser`). |
| `filter_mode` | enum | `LINEAR` | `NEAREST/LINEAR/TRILINEAR` (`TextFilter`). |
| `anisotropy` | float 0–16 | `4.0` | Анизотропия сэмплинга атласа. |
| `shader` | resource path | `"text"` | Оверрайд кастомного текстового шейдера. |

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
| `svg_path` | resource path | `""` | Векторный файл; фильтр `SVG (*.svg)`. |
| `color` | color4 | `[1,1,1,1]` | Тинт. |
| `flip_x` | bool | `False` | Горизонтальный флип. |
| `flip_y` | bool | `True` | Вертикальный флип (компенсация SVG Y-down). |
| `pixels_per_unit` | float 0.1–4096 | `100.0` | Масштаб растра (`ppu`); ключ кэша тесселяции — `(path, ppu)` (`svgr/tess.py:91-94`). |

Растр идёт через `QSvgRenderer` (`svgr/parse.py:18-24`); per-sprite флип загружается как `[flip_x, flip_y]` (`svgr/renderer.py:203,290`).

```python
sr = SvgRenderer()
sr.svg_path = "assets/vectors/logo.svg"
sr.pixels_per_unit = 100.0
sr.flip_y = True
entity.add_component(sr)
```

## Related pages

- [SpriteRenderer](../sprite-renderer.md), [SvgRenderer](../svg-renderer.md), [TextRenderer](../text-renderer.md), [VideoRenderer](../video-renderer.md), [Каталог шейдеров](./shader-catalog.md)

## Verified against

- `core/components/rendering/renderers/video_renderer.py:11-89` (поля, дефолты, сериализация)
- `core/components/rendering/renderers/text_renderer.py:29-112` (поля, `atlas_resolution = 128`, `anisotropy = 4.0`, `shader = "text"`, `font_world_space = True`)
- `core/components/rendering/renderers/svg_renderer.py:12-72` (`flip_y = True`, `pixels_per_unit = 100.0`)
- `core/shaders/internal/Video.shader:7-15`, `core/shaders/internal/Text.shader:7-15`
- `core/renderer/collector/snapshot.py:258-294`, `core/renderer/videor/renderer_gl.py`, `core/renderer/svgr/parse.py:18-24`, `core/renderer/svgr/tess.py:91-94`
