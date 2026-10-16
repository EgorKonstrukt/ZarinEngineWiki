# Depth of Field

> Depth-based focus blur. Circle-of-confusion from focal distance/range, aperture and blur size; Gaussian gather or polygonal Bokeh (blade count / curvature / rotation). Sky (depth >= 1.0) is treated as fully blurred background. Class `DepthOfField` in `core/components/rendering/postfx/depth_of_field.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Mode | `_mode` | ENUM DoFMode {Gaussian, Bokeh} | — | `Gaussian` |
| Focal Distance | `_focal_distance` | SLIDER | 0.1 – 200.0 | `10.0` |
| Focal Range | `_focal_range` | SLIDER | 0.001 – 100.0 | `8.0` |
| Aperture | `_aperture` | SLIDER | 0.0 – 5.0 | `1.0` |
| Max Blur Size | `_max_blur_size` | SLIDER | 1.0 – 50.0 | `12.0` |
| Bokeh Highlight Boost | `_bokeh_boost` | SLIDER | 0.0 – 3.0 | `0.5` |
| Foreground Blur Scale | `_foreground_scale` | SLIDER | 1.0 – 5.0 | `2.0` |
| Rings | `_ring_count` | INT_SLIDER | 1 – 5 | `3` |
| Blade Count | `_blade_count` | INT_SLIDER | 3 – 9 | `6` |
| Curvature | `_blade_curvature` | SLIDER | 0.0 – 1.0 | `1.0` |
| Rotation | `_blade_rotation` | SLIDER | 0.0 – 360.0 | `0.0` |
| Visualize CoC | `_visualize_coc` | BOOL | — | `False` |

## Usage

Editor: select the PostFX entity, Add Component > `DepthOfField`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.depth_of_field import DepthOfField, DoFMode
fx = DepthOfField()
entity.add_component(fx)
fx._mode = DoFMode.BOKEH
fx._focal_distance = 12.0
fx._aperture = 1.5
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/depth_of_field.py (DOF_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform sampler2D u_depth_tex;
uniform mat4 u_inv_proj;
uniform float u_focal_distance;
uniform float u_focal_range;
uniform float u_aperture;
uniform int u_mode;  // 0 Gaussian, 1 Bokeh
```


## Performance notes

Cost scales with Rings (1–5, inner loop r*8 taps, up to 64 per ring) × Max Blur Size. Pixels with |CoC| < 0.5 early-out.

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/depth_of_field.py` — class `DepthOfField`, `_inspector_fields`, `__init__` defaults, `render_type = "screen", _skip_rate = 0`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
