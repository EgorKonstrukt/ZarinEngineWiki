# Pixelate

> Retro mosaic. UVs are quantized to pixel_size blocks relative to the render resolution. Class `Pixelate` in `core/components/rendering/postfx/pixelate.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Pixel Size | `_pixel_size` | FLOAT | 1.0 – 256.0 | `8.0` |

## Usage

Editor: select the PostFX entity, Add Component > `Pixelate`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.pixelate import Pixelate
fx = Pixelate()
entity.add_component(fx)
fx._pixel_size = 12.0
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/pixelate.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_resolution;
uniform float u_pixel_size;
```


## Performance notes

Single pass, resolution-independent cost.

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/pixelate.py` — class `Pixelate`, `_inspector_fields`, `__init__` defaults, `Fullscreen nearest-block pass (u_resolution)`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
