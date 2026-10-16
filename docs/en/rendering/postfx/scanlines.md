# Scanlines

> CRT overlay: horizontal band darkening at frequency, barrel curvature warp, corner vignette and per-channel RGB offset. Class `Scanlines` in `core/components/rendering/postfx/scanlines.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 1.0 | `0.3` |
| Frequency | `_frequency` | FLOAT | 1.0 – 500.0 | `200.0` |
| Curvature | `_curvature` | FLOAT | 0.0 – 1.0 | `0.1` |
| Vignette | `_vignette` | FLOAT | 0.0 – 1.0 | `0.3` |
| RGB Offset | `_rgb_offset` | FLOAT | 0.0 – 0.05 | `0.002` |

## Usage

Editor: select the PostFX entity, Add Component > `Scanlines`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.scanlines import Scanlines
fx = Scanlines()
entity.add_component(fx)
fx._frequency = 240.0
fx._intensity = 0.4
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/scanlines.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_resolution;
uniform float u_intensity;
uniform float u_frequency;
uniform float u_curvature;
```


## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/scanlines.py` — class `Scanlines`, `_inspector_fields`, `__init__` defaults, `Fullscreen CRT overlay (u_resolution)`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
