# SSR

> Screen-space reflections. View-space raymarch (steps/thickness/max distance) with screen-edge and distance fade plus Fresnel weighting (power/strength); result composited over the frame by intensity. Class `SSR` in `core/components/rendering/postfx/ssr.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | SLIDER | 0.0 – 2.0 | `0.5` |
| Steps | `_steps` | INT_SLIDER | 8 – 64 | `32` |
| Thickness | `_thickness` | SLIDER | 0.05 – 4.0 | `1.5` |
| Max Distance | `_max_distance` | SLIDER | 10.0 – 200.0 | `60.0` |
| Edge Fade | `_edge_fade` | SLIDER | 0.02 – 0.3 | `0.12` |
| Distance Fade | `_distance_fade` | SLIDER | 0.3 – 0.95 | `0.6` |
| Fresnel Power | `_fresnel_power` | SLIDER | 1.0 – 8.0 | `4.0` |
| Fresnel Strength | `_fresnel_strength` | SLIDER | 0.0 – 1.0 | `0.05` |
| Resolution Scale | `_performance_scale` | SLIDER | 0.25 – 1.0 | `0.5` |

## Usage

Editor: select the PostFX entity, Add Component > `SSR`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.ssr import SSR
fx = SSR()
entity.add_component(fx)
fx._steps = 48
fx._max_distance = 80.0
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/ssr.py`.

```glsl
#version 330 core
uniform sampler2D u_depth_tex;
uniform sampler2D u_input_tex;
uniform mat4 u_proj;
uniform mat4 u_inv_proj;
uniform vec2 u_pixel_size;
// + u_steps, u_thickness, u_max_distance, u_edge_fade, u_distance_fade, u_fresnel_power, u_fresnel_strength, u_intensity
```


## Performance notes

Cost scales with Steps (8–64) × (Resolution Scale)² (default 0.5).

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/ssr.py` — class `SSR`, `_inspector_fields`, `__init__` defaults, `Depth raymarch + composite. Seeded kernel not used; uses proj/inv_proj + pixel size.`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
