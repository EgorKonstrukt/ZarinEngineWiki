# Video Renderer
> In-world video quad: `VideoRenderer` (`core/components/rendering/renderers/video_renderer.py`), decoded by `core/renderer/videor/player.py`, drawn by `renderer_gl.py`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`_updates = True` (per-frame decode). Optional `audio_source_entity_id` links an audio source entity. Player shells to ffmpeg with `-vsync drop` (`videor/player.py:207`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `video_path` | resource path (mp4/avi/mov/mkv/webm) | `""` | Clip file (`video_renderer.py:22,36`). |
| `audio_source_entity_id` | gameobject id | `""` | Linked audio source (`video_renderer.py:23,37`). |
| `color` | color4 | `[1, 1, 1, 1]` | Tint (`video_renderer.py:24,38`). |
| `flip_x` | bool | `False` | Mirror horizontally (`video_renderer.py:26,39`). |
| `flip_y` | bool | `False` | Mirror vertically (`video_renderer.py:27,40`). |
| `play_on_start` | bool | `True` | Autoplay (`video_renderer.py:28,41`). |
| `loop` | bool | `True` | Loop (`video_renderer.py:29,42`). |
| `volume` | float (0–1) | `1.0` | Volume (`video_renderer.py:30,43`). |
| `offset` | float ≥0 | `0.0` | Start offset, s (`video_renderer.py:31,44`). |
## Usage in Editor
Assign Video, enable Play on Start + Loop for ambient screens. Link an Audio Source entity for sound; set Offset to skip intros.
## Usage in Code
```python
vr = entity.get_component(VideoRenderer)
vr.video_path = "assets/videos/intro.mp4"
vr.loop = False
vr.volume = 0.8
```
## Related pages
- [Sprite Renderer](./sprite-renderer.md), [Text Renderer](./text-renderer.md)
## Verified against
- `core/components/rendering/renderers/video_renderer.py:18-89`
- `core/renderer/videor/player.py:207`, `core/renderer/videor/renderer_gl.py:73-136`
