# FXAA

> Fast approximate anti-aliasing. Luma edge detection with subpixel blend, contrast-based early-out (reduce / reduce_min) and max span clamp. Class `FXAA` in `core/components/rendering/postfx/fxaa.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Subpixel | `_subpix` | FLOAT | 0.0 – 1.0 | `1.0` |
| Reduce | `_reduce` | FLOAT | 0.0 – 1.0 | `0.125` |
| Reduce Min | `_reduce_min` | FLOAT | 0.0 – 0.1 | `0.0078125` |
| Span | `_span` | FLOAT | 1.0 – 16.0 | `8.0` |

## Usage

Editor: select the PostFX entity, Add Component > `FXAA`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.fxaa import FXAA
fx = FXAA()
entity.add_component(fx)
fx._subpix = 0.75
fx._span = 8.0
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/fxaa.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_pixel_size;
uniform float u_subpix;
uniform float u_reduce;
uniform float u_reduce_min;
```


## Performance notes

Single pass, fixed tap count; Span (1–16) bounds the edge search.

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/fxaa.py` — class `FXAA`, `_inspector_fields`, `__init__` defaults, `Single fullscreen luma-edge AA pass`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
