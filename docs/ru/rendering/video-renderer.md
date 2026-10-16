# Видео-рендерер
> Видео-квад в мире: `VideoRenderer` (`core/components/rendering/renderers/video_renderer.py`), декодирует `core/renderer/videor/player.py`, рисует `renderer_gl.py`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`_updates = True` (декодирование каждый кадр). Опциональный `audio_source_entity_id` связывает сущность-источник звука. Плеер вызывает ffmpeg с `-vsync drop` (`videor/player.py:207`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `video_path` | путь (mp4/avi/mov/mkv/webm) | `""` | Файл клипа (`video_renderer.py:22,36`). |
| `audio_source_entity_id` | gameobject id | `""` | Связанный источник звука (`video_renderer.py:23,37`). |
| `color` | color4 | `[1, 1, 1, 1]` | Тинт (`video_renderer.py:24,38`). |
| `flip_x` | bool | `False` | Зеркало по горизонтали (`video_renderer.py:26,39`). |
| `flip_y` | bool | `False` | Зеркало по вертикали (`video_renderer.py:27,40`). |
| `play_on_start` | bool | `True` | Автоплей (`video_renderer.py:28,41`). |
| `loop` | bool | `True` | Зацикливание (`video_renderer.py:29,42`). |
| `volume` | float (0–1) | `1.0` | Громкость (`video_renderer.py:30,43`). |
| `offset` | float ≥0 | `0.0` | Стартовый сдвиг, с (`video_renderer.py:31,44`). |
## Usage in Editor
Назначить Video, для фоновых экранов — Play on Start + Loop. Для звука связать Audio Source; Offset пропускает интро.
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
