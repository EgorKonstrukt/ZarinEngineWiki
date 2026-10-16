# Sobel

> Luma Sobel with 5 composite modes (int 0–4): 0 scene→edge color, 1 white→edge color, 2 scene→black, 3 gradient-direction color, 4+ glow add. Thickness scales the 3×3 offsets; threshold gates via smoothstep(threshold, threshold+0.5). Class `Sobel` in `core/components/rendering/postfx/sobel.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Mode | `_mode` | INT | 0 – 4 | `0` |
| Threshold | `_threshold` | FLOAT | 0.0 – 2.0 | `0.1` |
| Thickness | `_thickness` | FLOAT | 0.5 – 8.0 | `1.0` |
| Intensity | `_intensity` | FLOAT | 0.0 – 3.0 | `1.0` |
| Edge Color | `_edge_color` | COLOR | — | `(0.0, 0.0, 0.0)` |

## Usage

Editor: select the PostFX entity, Add Component > `Sobel`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.sobel import Sobel
fx = Sobel()
entity.add_component(fx)
fx._mode = 0
fx._thickness = 1.5
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/sobel.py (SOBEL_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_pixel_size;
uniform float u_threshold;
uniform float u_thickness;
uniform float u_intensity;
uniform vec3 u_edge_color;
uniform int u_mode;
```


## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/sobel.py` — class `Sobel`, `_inspector_fields`, `__init__` defaults, `render_type = "screen", _intensity_prop = "_intensity"`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
