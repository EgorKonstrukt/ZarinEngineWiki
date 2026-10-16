# Directional Blur

> Linear multi-tap blur along a 2D direction vector. Tap count and spread from samples / strength. Class `DirectionalBlur` in `core/components/rendering/postfx/directional_blur.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Direction | `_direction` | VEC2 | -1.0 – 1.0 | `(1.0, 0.0)` |
| Strength | `_strength` | SLIDER | 0.0 – 64.0 | `8.0` |
| Samples | `_samples` | INT_SLIDER | 4 – 64 | `16` |

## Usage

Editor: select the PostFX entity, Add Component > `DirectionalBlur`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.directional_blur import DirectionalBlur
fx = DirectionalBlur()
entity.add_component(fx)
fx._strength = 12.0
fx._samples = 24
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/directional_blur.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_direction;
uniform float u_strength;
uniform vec2 u_pixel_size;
uniform int u_samples;
```


## Performance notes

Cost scales with Samples (4–64).

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/directional_blur.py` — class `DirectionalBlur`, `_inspector_fields`, `__init__` defaults, `GraphicsEffect base, fullscreen pass`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
