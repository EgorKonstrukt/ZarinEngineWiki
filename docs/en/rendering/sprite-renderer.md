# Sprite Renderer
> Camera-facing quad: `SpriteRenderer` (`core/components/rendering/renderers/sprite_renderer.py`), drawn by `core/renderer/spr/`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Single texture + RGBA tint; 3-element colors auto-gain alpha `1.0` (`sprite_renderer.py:38-47`). No lighting, no shadows.
## Properties
| name | type | default | description |
|---|---|---|---|
| `texture_path` | resource path (png/jpg/jpeg) | `""` | Sprite image (`sprite_renderer.py:21,29`). |
| `color` | color4 | `[1, 1, 1, 1]` | Tint × texture (`sprite_renderer.py:22,30`). |
| `flip_x` | bool | `False` | Mirror horizontally (`sprite_renderer.py:23,31`). |
| `flip_y` | bool | `False` | Mirror vertically (`sprite_renderer.py:24,32`). |
## Usage in Editor
Add Component → Sprite Renderer, assign Texture, tint via Color. Use Flip for mirroring without duplicating assets.
## Usage in Code
```python
sp = entity.get_component(SpriteRenderer)
sp.texture_path = "assets/textures/coin.png"
sp.color = [1.0, 0.9, 0.5, 1.0]
sp.flip_x = True
```
## Related pages
- [SVG Renderer](./svg-renderer.md), [Text Renderer](./text-renderer.md), [Video Renderer](./video-renderer.md), [Materials](./materials.md)
## Verified against
- `core/components/rendering/renderers/sprite_renderer.py:17-67`
- `core/renderer/spr/sprites.py`, `core/renderer/spr/batch.py`
