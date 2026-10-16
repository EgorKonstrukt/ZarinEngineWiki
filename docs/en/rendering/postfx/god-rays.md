# God Rays

> Light-shaft raymarch. Bright pass above threshold is scattered toward the light screen position over N samples, scaled by exposure × intensity. Class `GodRays` in `core/components/rendering/postfx/godrays/component.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 5.0 | `0.8` |
| Exposure | `_exposure` | FLOAT | 0.0 – 0.5 | `0.01` |
| Threshold | `_threshold` | SLIDER | 0.05 – 0.5 | `0.25` |
| Samples | `_num_samples` | INT | 8 – 256 | `64` |

## Usage

Editor: select the PostFX entity, Add Component > `GodRays`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.godrays.component import GodRays
fx = GodRays()
entity.add_component(fx)
fx._intensity = 1.2
fx._num_samples = 96
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/godrays/shaders.py`.

```glsl
// GOD_RAYS frag uniforms (exact names):
// uniform sampler2D u_scene_color; uniform sampler2D u_depth_tex;
// uniform vec2 u_light_uv; uniform vec3 u_light_color; uniform float u_light_intensity;
// uniform float u_threshold; uniform float u_intensity; uniform float u_exposure; uniform int u_num_samples;
```


## Performance notes

Cost scales linearly with Samples (8–256, default 64). Uses scratch color/depth targets (`_scratch_color`, `_scratch_depth`).

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/godrays/component.py` — class `GodRays`, `_inspector_fields`, `__init__` defaults, `Radial raymarch from light UV; occluded by depth (u_depth_tex)`.

- `core/components/rendering/postfx/godrays/shaders.py`
- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
