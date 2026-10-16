# SSAO

> Screen-space ambient occlusion from depth + projection. Hemisphere samples within radius are depth-compared (bias/power/intensity shape the darkening), then edge-aware bilateral blur smooths while preserving depth discontinuities. Class `SSAO` in `core/components/rendering/postfx/ssaop/component.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | SLIDER | 0.0 – 2.0 | `1.0` |
| Radius | `_radius` | SLIDER | 0.1 – 5.0 | `1.0` |
| Power | `_power` | SLIDER | 0.1 – 5.0 | `2.0` |
| Bias | `_bias` | SLIDER | 0.0 – 0.1 | `0.025` |
| Samples | `_kernel_size` | INT_SLIDER | 4 – 64 | `32` |
| Resolution Scale | `_performance_scale` | SLIDER | 0.25 – 1.0 | `0.5` |
| Blur Radius | `_bilateral_radius` | SLIDER | 1.0 – 6.0 | `3.0` |
| Edge Sensitivity | `_bilateral_sigma` | SLIDER | 1.0 – 200.0 | `50.0` |

## Usage

Editor: select the PostFX entity, Add Component > `SSAO`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.ssaop.component import SSAO
fx = SSAO()
entity.add_component(fx)
fx._radius = 1.5
fx._kernel_size = 32
fx._performance_scale = 0.5
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/ssaop/shaders.py`.

```glsl
// SSAO uniforms (exact names):
// uniform sampler2D u_depth_tex; uniform mat4 u_proj; uniform mat4 u_inv_proj;
// uniform vec2 u_pixel_size; uniform vec2 u_resolution;
// uniform float u_radius; uniform float u_power; uniform float u_bias; uniform float u_intensity;
// composite: uniform sampler2D u_ao_tex; uniform sampler2D u_input_tex; uniform sampler2D u_noise_tex;
// uniform float u_bilateral_radius; uniform float u_bilateral_sigma;
```


## Performance notes

Cost scales with Samples (4–64) × (Resolution Scale)² (default 0.5 = quarter res) + bilateral blur radius (1–6).

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/ssaop/component.py` — class `SSAO`, `_inspector_fields`, `__init__` defaults, `Depth-only AO: hemisphere kernel gather + bilateral blur composite. Kernel generated with fixed seed (NP_RNG = RandomState(42)).`.

- `core/components/rendering/postfx/ssaop/shaders.py`
- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
