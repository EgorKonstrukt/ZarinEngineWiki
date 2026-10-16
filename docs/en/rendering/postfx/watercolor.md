# Watercolor

> Paper-paint look. Neighbourhood mean (brush_size taps) is edge-darkened and wet-blended: wetness controls pigment pooling, intensity blends to the photo. Class `Watercolor` in `core/components/rendering/postfx/watercolor.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Brush Size | `_brush_size` | FLOAT | 1.0 – 8.0 | `4.0` |
| Edge Darken | `_edge_darken` | FLOAT | 0.0 – 2.0 | `0.8` |
| Wetness | `_wetness` | FLOAT | 0.0 – 1.0 | `0.5` |
| Intensity | `_intensity` | FLOAT | 0.0 – 1.0 | `1.0` |

## Usage

Editor: select the PostFX entity, Add Component > `Watercolor`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.watercolor import Watercolor
fx = Watercolor()
entity.add_component(fx)
fx._brush_size = 5.0
fx._wetness = 0.6
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/watercolor.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_pixel_size;
uniform float u_brush_size;
uniform float u_edge_darken;
uniform float u_wetness;
```


## Performance notes

Tap count grows with Brush Size (1–8).

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/watercolor.py` — class `Watercolor`, `_inspector_fields`, `__init__` defaults, `Fullscreen painterly pass (u_pixel_size)`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
