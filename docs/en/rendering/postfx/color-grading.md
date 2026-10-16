# Color Grading

> Exposure / white-balance / contrast / saturation with selectable tonemapper (Off, ACES, Reinhard, Neutral). Order in shader: exposure → white balance → tonemap → contrast → saturation. Class `ColorGrading` in `core/components/rendering/postfx/color_grading.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Exposure (EV) | `_exposure` | FLOAT | -5.0 – 5.0 | `0.0` |
| Contrast | `_contrast` | FLOAT | 0.0 – 3.0 | `1.0` |
| Saturation | `_saturation` | FLOAT | 0.0 – 3.0 | `1.0` |
| Temperature | `_temperature` | FLOAT | -1.0 – 1.0 | `0.0` |
| Tint | `_tint` | FLOAT | -1.0 – 1.0 | `0.0` |
| Tonemapping | `_tonemap_mode` | ENUM TonemapMode {Off, ACES, Reinhard, Neutral} → int 0–3 | — | `Off (0)` |

## Usage

Editor: select the PostFX entity, Add Component > `ColorGrading`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.color_grading import ColorGrading, TonemapMode
fx = ColorGrading()
entity.add_component(fx)
fx._exposure = 0.5
fx._contrast = 1.1
fx._tonemap_mode = TonemapMode.ACES
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/color_grading.py (CG_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_exposure;
uniform float u_contrast;
uniform float u_saturation;
uniform float u_temperature;
uniform float u_tint;
uniform int u_tonemap_mode;  // 0 Off, 1 ACES, 2 Reinhard, 3 Neutral
```


## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/color_grading.py` — class `ColorGrading`, `_inspector_fields`, `__init__` defaults, `render_type = "screen". PostPassMixin._has_tonemap_effect() returns True when an enabled active ColorGrading exists, switching the present path to copy instead of the fallback tonemap program.`.

- `core/renderer/post_pass.py`
- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
