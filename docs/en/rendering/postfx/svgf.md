# SVGF

> Spatiotemporal variance-guided denoiser. Reprojection reuses history gated by stability/disocclusion; à-trous passes filter guided by depth/normal/luma phi weights; final composites. Class `SpatiotemporalVarianceGuidedFilter` in `core/components/rendering/postfx/svgfp/component.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Temporal Stability | `_stability` | FLOAT | 0.0 – 0.99 | `0.9` |
| Disocclusion Depth | `_disocclusion` | FLOAT | 0.0 – 1.0 | `0.08` |
| Phi Depth | `_phi_depth` | FLOAT | 0.01 – 10.0 | `1.0` |
| Phi Normal | `_phi_normal` | FLOAT | 1.0 – 512.0 | `128.0` |
| Phi Luminance | `_phi_luma` | FLOAT | 0.1 – 16.0 | `4.0` |
| Iterations | `_iterations` | INT | 1 – 8 | `5` |

## Usage

Editor: select the PostFX entity, Add Component > `SpatiotemporalVarianceGuidedFilter`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.svgfp.component import SpatiotemporalVarianceGuidedFilter
fx = SpatiotemporalVarianceGuidedFilter()
entity.add_component(fx)
fx._stability = 0.92
fx._iterations = 5
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/svgfp/reproj.py (SVGF_REPROJ_FRAG), atrous.py (SVGF_ATROUS_FRAG), final.py (SVGF_FINAL_FRAG)`.

```glsl
// REPROJ: uniform sampler2D u_current; uniform sampler2D u_history; uniform sampler2D u_velocity; uniform float u_stability; uniform float u_disocclusion;
// ATROUS: uniform sampler2D u_color; uniform sampler2D u_depth; uniform sampler2D u_moment; uniform float u_phi_depth; uniform float u_phi_normal; uniform float u_phi_luma; uniform int u_step_size;
// FINAL: uniform sampler2D u_input;
```


## Performance notes

Cost scales with Iterations (1–8, default 5) à-trous passes + history/moment buffers. History invalidated on resize (`_history_valid`).

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/svgfp/component.py` — class `SpatiotemporalVarianceGuidedFilter`, `_inspector_fields`, `__init__` defaults, `Temporal reprojection → à-trous wavelet (N iterations) → composite. Keeps history + moments + 2 atrous buffers with frame counter.`.

- `core/components/rendering/postfx/svgfp/reproj.py`
- `core/components/rendering/postfx/svgfp/atrous.py`
- `core/components/rendering/postfx/svgfp/final.py`
- `core/components/rendering/postfx/svgfp/vert.py`
- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
