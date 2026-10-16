# Quantize

> Posterize. Each channel is rounded to 2^bit_depth levels, then mixed with the source by intensity. Class `Quantize` in `core/components/rendering/postfx/quantize.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Bit Depth | `_bit_depth` | FLOAT | 1.0 – 8.0 | `4.0` |
| Intensity | `_intensity` | FLOAT | 0.0 – 1.0 | `1.0` |

## Usage

Editor: select the PostFX entity, Add Component > `Quantize`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.quantize import Quantize
fx = Quantize()
entity.add_component(fx)
fx._bit_depth = 3.0
fx._intensity = 1.0
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/quantize.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_bit_depth;
uniform float u_intensity;
```


## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/quantize.py` — class `Quantize`, `_inspector_fields`, `__init__` defaults, `Fullscreen color-level pass`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
