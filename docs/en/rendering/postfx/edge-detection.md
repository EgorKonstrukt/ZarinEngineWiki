# Edge Detection

> Luma Sobel 3×3 edge overlay. Edge magnitude (Rec.709-ish luma weights 0.2126/0.7152/0.0722) is scaled by intensity and mixed toward the edge color. NOTE: the `_outline_only` Inspector toggle is serialized but the current EDGE_FRAG always mixes over the scene (no outline-only branch in the shipped shader). Class `EdgeDetection` in `core/components/rendering/postfx/edge_detection.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 10.0 | `2.0` |
| Edge Color | `_edge_color` | COLOR | — | `(0.0, 0.0, 0.0)` |
| Outline Only | `_outline_only` | BOOL | — | `False (no shader branch; stored only)` |

## Usage

Editor: select the PostFX entity, Add Component > `EdgeDetection`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.edge_detection import EdgeDetection
fx = EdgeDetection()
entity.add_component(fx)
fx._intensity = 2.0
fx._edge_color = (0.0, 0.0, 0.0)
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/edge_detection.py (EDGE_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform vec3 u_edge_color;
uniform vec2 u_pixel_size;
// edge = clamp(length(vec2(gx, gy)) * u_intensity, 0, 1); bg = mix(c, u_edge_color, edge);
```


## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/edge_detection.py` — class `EdgeDetection`, `_inspector_fields`, `__init__` defaults, `render_type = "screen", _intensity_prop = "_intensity" (auto-skipped at ≤ 0.001)`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
