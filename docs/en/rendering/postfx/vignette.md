# Vignette

> Radial edge darkening/tint. Distance from center shaped by smoothness (falloff) and roundness (aspect compensation), mixed toward color by intensity. Class `Vignette` in `core/components/rendering/postfx/vignette.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 5.0 | `0.5` |
| Smoothness | `_smoothness` | FLOAT | 0.0 – 1.0 | `1.0` |
| Roundness | `_roundness` | FLOAT | 0.0 – 2.0 | `1.0` |
| Color | `_color` | COLOR | — | `(0.0, 0.0, 0.0)` |

## Usage

Editor: select the PostFX entity, Add Component > `Vignette`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.vignette import Vignette
fx = Vignette()
entity.add_component(fx)
fx._intensity = 0.7
fx._smoothness = 0.8
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/vignette.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform float u_smoothness;
uniform float u_roundness;
uniform vec2 u_center;
// + uniform vec3 u_color;
```


## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/vignette.py` — class `Vignette`, `_inspector_fields`, `__init__` defaults, `Fullscreen radial mask pass (u_center is internal, fixed at screen center)`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
