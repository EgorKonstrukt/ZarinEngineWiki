# Dithering

> Ordered dither + color-depth quantization. Bit depth sets posterization levels; optional grayscale; intensity blends the effect. Class `Dithering` in `core/components/rendering/postfx/dithering.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 2.0 | `1.0` |
| Bit Depth | `_bit_depth` | FLOAT | 1.0 – 8.0 | `6.0` |
| Grayscale | `_grayscale` | BOOL | — | `False` |

## Usage

Editor: select the PostFX entity, Add Component > `Dithering`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.dithering import Dithering
fx = Dithering()
entity.add_component(fx)
fx._bit_depth = 4.0
fx._intensity = 1.0
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/dithering.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform float u_bit_depth;
uniform float u_grayscale;
```


## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/dithering.py` — class `Dithering`, `_inspector_fields`, `__init__` defaults, `GraphicsEffect base, fullscreen pass`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
