# Lens Distortion

> Barrel/pincushion warp with per-channel scaling (chromatic fringe) and center scale compensation. Class `LensDistortion` in `core/components/rendering/postfx/lens_distortion.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Strength | `_strength` | FLOAT | -1.0 – 1.0 | `0.3` |
| Chromatic | `_chromatic` | FLOAT | 0.0 – 1.0 | `0.5` |
| Scale | `_scale` | FLOAT | 0.5 – 1.5 | `0.9` |

## Usage

Editor: select the PostFX entity, Add Component > `LensDistortion`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.lens_distortion import LensDistortion
fx = LensDistortion()
entity.add_component(fx)
fx._strength = 0.35
fx._chromatic = 0.4
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/lens_distortion.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_strength;
uniform float u_chromatic;
uniform float u_scale;
```


## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/lens_distortion.py` — class `LensDistortion`, `_inspector_fields`, `__init__` defaults, `Fullscreen UV warp pass`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
