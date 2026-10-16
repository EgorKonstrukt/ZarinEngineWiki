# Panini

> Panini wide-FOV projection. Remaps UVs to reduce edge stretching on ultrawide views; distance controls curvature, crop_to_fit blends crop vs fit. Class `PaniniProjection` in `core/components/rendering/postfx/panini.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Distance | `_distance` | FLOAT | 0.0 – 1.0 | `0.5` |
| Crop to Fit | `_crop_to_fit` | FLOAT | 0.0 – 1.0 | `1.0` |

## Usage

Editor: select the PostFX entity, Add Component > `PaniniProjection`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.panini import PaniniProjection
fx = PaniniProjection()
entity.add_component(fx)
fx._distance = 0.6
fx._crop_to_fit = 1.0
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/panini.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_view_extents;
uniform float u_distance;
uniform float u_crop_scale;
```


## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/panini.py` — class `PaniniProjection`, `_inspector_fields`, `__init__` defaults, `Fullscreen UV reprojection (u_view_extents)`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
