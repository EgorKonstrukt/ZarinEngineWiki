# Kuwahara

> Oil-painting filter. Per-pixel quadrant variance picks the flattest region mean; intensity blends back to the photo. Class `Kuwahara` in `core/components/rendering/postfx/kuwahara.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Radius | `_radius` | FLOAT | 1.0 – 4.0 | `2.0` |
| Intensity | `_intensity` | FLOAT | 0.0 – 1.0 | `1.0` |

## Usage

Editor: select the PostFX entity, Add Component > `Kuwahara`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.kuwahara import Kuwahara
fx = Kuwahara()
entity.add_component(fx)
fx._radius = 3.0
fx._intensity = 0.8
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/kuwahara.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_radius;
uniform float u_intensity;
uniform vec2 u_pixel_size;
```


## Performance notes

Tap count grows with Radius² (1–4).

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/kuwahara.py` — class `Kuwahara`, `_inspector_fields`, `__init__` defaults, `Fullscreen anisotropic smoothing pass`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
