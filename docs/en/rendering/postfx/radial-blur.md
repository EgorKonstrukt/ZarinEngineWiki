# Radial Blur

> Zoom/spin blur around a screen center. Samples gather along center→pixel vectors; low-motion pixels under threshold keep the sharp source. Class `RadialBlur` in `core/components/rendering/postfx/radial_blur.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Center | `_center` | VEC2 | 0.0 – 1.0 | `(0.5, 0.5)` |
| Strength | `_strength` | SLIDER | 0.0 – 0.5 | `0.05` |
| Samples | `_samples` | INT_SLIDER | 4 – 64 | `16` |
| Threshold | `_threshold` | SLIDER | 0.0 – 0.01 | `0.001` |

## Usage

Editor: select the PostFX entity, Add Component > `RadialBlur`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.radial_blur import RadialBlur
fx = RadialBlur()
entity.add_component(fx)
fx._strength = 0.08
fx._samples = 24
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/radial_blur.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform sampler2D u_velocity_tex;
uniform vec2 u_center;
uniform float u_strength;
uniform float u_threshold;
```


## Performance notes

Cost scales with Samples (4–64).

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/radial_blur.py` — class `RadialBlur`, `_inspector_fields`, `__init__` defaults, `Center-anchored gather; optional velocity path (u_velocity_tex / u_use_velocity)`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
