# Lens Flares

> Ghost/streak flares. Luminance above threshold (with soft knee) is extracted, blurred, then composited as scaled ghosts, glow and horizontal anamorphic streak with chromatic offset. Class `LensFlares` in `core/components/rendering/postfx/lensp/component.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 5.0 | `1.0` |
| Scale | `_scale` | FLOAT | 0.0 – 5.0 | `1.0` |
| Glow | `_glow` | FLOAT | 0.0 – 5.0 | `1.0` |
| Bright Threshold | `_threshold` | FLOAT | 0.0 – 2.0 | `1.0` |
| Soft Knee | `_soft_threshold` | FLOAT | 0.0 – 1.0 | `0.5` |
| Ghost Intensity | `_ghost_intensity` | FLOAT | 0.0 – 5.0 | `1.0` |
| Anamorphic Streak | `_anamorphic` | FLOAT | 0.0 – 5.0 | `0.6` |
| Chromatic | `_chromatic` | FLOAT | 0.0 – 3.0 | `0.8` |

## Usage

Editor: select the PostFX entity, Add Component > `LensFlares`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.lensp.component import LensFlares
fx = LensFlares()
entity.add_component(fx)
fx._threshold = 1.2
fx._anamorphic = 1.0
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/lensp/shaders.py (FLARE_* / EXTRACT_* )`.

```glsl
// flare uniforms (exact names):
// uniform sampler2D u_scene_color; uniform sampler2D u_bright_tex; uniform sampler2D u_glow_tex;
// uniform float u_intensity; uniform float u_scale; uniform float u_glow;
// uniform float u_threshold; uniform float u_soft_threshold;
// uniform float u_ghost_intensity; uniform float u_anamorphic; uniform float u_chromatic;
```


## Performance notes

Bright-extract + downsample chain; cost scales with internal blur resolution, not with threshold.

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/lensp/component.py` — class `LensFlares`, `_inspector_fields`, `__init__` defaults, `Bright-extract → downsample → flare composite (ghosts + anamorphic streak)`.

- `core/components/rendering/postfx/lensp/shaders.py`
- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
