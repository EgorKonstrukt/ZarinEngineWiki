# Motion Blur

> Camera/object motion gather. Each pixel is reprojected (velocity buffer or prev/inv view-proj + depth) and accumulated over N samples along the motion vector; pixels below threshold are left sharp. Class `MotionBlur` in `core/components/rendering/postfx/motion_blur.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Samples | `_samples` | INT_SLIDER | 4 – 64 | `16` |
| Strength | `_strength` | SLIDER | 0.0 – 1.5 | `1.0` |
| Threshold | `_threshold` | SLIDER | 0.0 – 0.01 | `0.001` |

## Usage

Editor: select the PostFX entity, Add Component > `MotionBlur`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.motion_blur import MotionBlur
fx = MotionBlur()
entity.add_component(fx)
fx._samples = 24
fx._strength = 0.8
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/motion_blur.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform sampler2D u_velocity_tex;
uniform sampler2D u_depth_tex;
uniform mat4 u_inv_view_proj;
uniform mat4 u_prev_view_proj;
// + u_samples, u_strength, u_threshold, u_use_velocity, u_use_velocity_tex
```


## Performance notes

Cost scales with Samples (4–64). Requires velocity or valid prev-frame matrices; falls back to depth reprojection.

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/motion_blur.py` — class `MotionBlur`, `_inspector_fields`, `__init__` defaults, `Velocity / view-projection reprojection gather. Uses u_velocity_tex when present, else reconstructs from u_prev_view_proj × u_inv_view_proj + depth.`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
