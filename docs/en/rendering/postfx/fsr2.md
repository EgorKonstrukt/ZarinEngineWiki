# FSR2

> Temporal upscaler chain. EASU upscales, the temporal pass reprojects with velocity/depth and variance clipping, RCAS sharpens. Stability/disocclusion control history reuse. Class `FidelityFXSuperResolution2` in `core/components/rendering/postfx/fsr2p/effect.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Temporal Stability | `_stability` | FLOAT | 0.0 – 0.99 | `0.9` |
| RCAS Attenuation | `_rcas_sharpness` | FLOAT | 0.0 – 1.0 | `0.2` |
| Disocclusion Depth | `_disocclusion` | FLOAT | 0.0 – 1.0 | `0.15` |
| Variance Clip Gamma | `_variance_gamma` | FLOAT | 0.5 – 4.0 | `1.25` |
| EASU Edge Sharpness | `_easu_sharpness` | FLOAT | 0.0 – 1.0 | `0.35` |

## Usage

Editor: select the PostFX entity, Add Component > `FidelityFXSuperResolution2`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.fsr2p.effect import FidelityFXSuperResolution2
fx = FidelityFXSuperResolution2()
entity.add_component(fx)
fx._stability = 0.9
fx._rcas_sharpness = 0.2
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/fsr2p/easu.py (EASU_FRAG), temporal.py (TEMPORAL_FRAG), rcas.py (RCAS_FRAG)`.

```glsl
// EASU: uniform sampler2D u_src; uniform vec2 u_src_size; uniform vec2 u_src_pixel; uniform float u_sharpness;
// TEMPORAL: uniform sampler2D u_current; uniform sampler2D u_history; uniform sampler2D u_velocity; uniform float u_stability; uniform float u_variance_gamma;
// RCAS: uniform sampler2D u_rcas_input; uniform vec2 u_rcas_size; uniform vec2 u_rcas_con;
```


## Performance notes

Three fullscreen passes + 2-frame history buffers. Internal `_history_valid` / `_cur_index` ping-pong; history invalidated on resize.

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/fsr2p/effect.py` — class `FidelityFXSuperResolution2`, `_inspector_fields`, `__init__` defaults, `Multi-pass chain: EASU (edge-adaptive upscale) → TEMPORAL (reprojection + variance clip) → RCAS (sharpen). Keeps 2-frame history (u_history) + variance gamma clip.`.

- `core/components/rendering/postfx/fsr2p/easu.py`
- `core/components/rendering/postfx/fsr2p/rcas.py`
- `core/components/rendering/postfx/fsr2p/temporal.py`
- `core/components/rendering/postfx/fsr2p/vert.py`
- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
