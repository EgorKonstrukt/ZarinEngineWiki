# Спрайт-рендерер
> Квад к камере: `SpriteRenderer` (`core/components/rendering/renderers/sprite_renderer.py`), рисует `core/renderer/spr/`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Одна текстура + RGBA-тинт; цвета из 3 элементов получают альфу `1.0` (`sprite_renderer.py:38-47`). Без освещения и теней.
## Properties
| name | type | default | description |
|---|---|---|---|
| `texture_path` | путь (png/jpg/jpeg) | `""` | Картинка (`sprite_renderer.py:21,29`). |
| `color` | color4 | `[1, 1, 1, 1]` | Тинт × текстура (`sprite_renderer.py:22,30`). |
| `flip_x` | bool | `False` | Зеркало по горизонтали (`sprite_renderer.py:23,31`). |
| `flip_y` | bool | `False` | Зеркало по вертикали (`sprite_renderer.py:24,32`). |
## Usage in Editor
Add Component → Sprite Renderer, назначить Texture, тинт через Color. Flip — зеркалирование без дублей ассетов.
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
