# Bloom

> Screen-space glow. Bright pixels above threshold are extracted (prefilter), blurred through a downsampled mip chain, then added back with optional dirt-lens tint. Class `Bloom` in `core/components/rendering/postfx/bloomp/__init__.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 5.0 | `1.0` |
| Threshold | `_threshold` | FLOAT | 0.0 – 5.0 | `1.5` |
| Max Color | `_max_color` | FLOAT | 0.5 – 10.0 | `3.8` |
| Downsample | `_downsample` | INT | 1 – 8 | `2` |
| Diffusion | `_diffusion` | FLOAT | 0.5 – 5.0 | `1.0` |
| Dirt Texture | `_dirt_texture` | RESOURCE_PATH (Images *.png *.jpg *.jpeg *.tga *.bmp) | — | `""` |
| Dirt Intensity | `_dirt_intensity` | FLOAT | 0.0 – 5.0 | `0.0` |

## Usage

Editor: select the PostFX entity, Add Component > `Bloom`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.bloomp import Bloom
fx = Bloom()
entity.add_component(fx)
fx._intensity = 1.0
fx._threshold = 1.5
fx._downsample = 2
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/bloomp/threshold.py (PREFILTER_FRAG), bloomp/blur.py (DOWN_FRAG / UP_FRAG), bloomp/composite.py (OUTPUT_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_threshold;
uniform float u_max_color;   // prefilter
// blur: uniform float u_diffusion; uniform sampler2D u_down_tex;
// composite: uniform sampler2D u_dirt_tex; uniform float u_dirt_intensity; uniform int u_has_dirt; uniform float u_intensity;
```


## Performance notes

Mip-chain cost scales with Downsample (1–8, default 2) and Diffusion. Skipped automatically when `_intensity <= 0.001` (`GraphicsEffect.should_skip`, `_intensity_prop = "_intensity"`).

## Related

Related: [PostFX Stack](../postfx.md), [Blur](blur.md), [Color Grading](color-grading.md).

## Verified against

- `core/components/rendering/postfx/bloomp/__init__.py` — class `Bloom`, `_inspector_fields`, `__init__` defaults, `render_type = "additive", _intensity_prop = "_intensity"`.

- `core/components/rendering/postfx/bloomp/threshold.py`
- `core/components/rendering/postfx/bloomp/blur.py`
- `core/components/rendering/postfx/bloomp/composite.py`
- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
