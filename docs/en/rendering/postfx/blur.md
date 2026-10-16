# Blur

> Separable fullscreen gaussian soften. Two passes per iteration (horizontal then vertical) with gaussian weights from radius. Class `Blur` in `core/components/rendering/postfx/blur.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Radius | `_radius` | FLOAT | 0.0 – 32.0 | `2.0` |
| Iterations | `_iterations` | INT | 1 – 8 | `1` |

## Usage

Editor: select the PostFX entity, Add Component > `Blur`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.blur import Blur
fx = Blur()
entity.add_component(fx)
fx._radius = 4.0
fx._iterations = 2
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/blur.py (BLUR_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_radius;
uniform vec2 u_pixel_size;
// int r = int(min(u_radius, 64.0)); sigma = max(u_radius * 0.5, 0.5);
```


## Performance notes

Cost scales with Radius (clamped to 64 in shader) × Iterations (1–8). Each iteration does a horizontal + vertical fullscreen pass via a temp FBO.

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/blur.py` — class `Blur`, `_inspector_fields`, `__init__` defaults, `render_type = "screen"`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
