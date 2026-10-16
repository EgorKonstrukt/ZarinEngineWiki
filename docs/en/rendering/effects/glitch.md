# Glitch

> Per-object block glitch. UV rows are shifted in block_size bands at speed, mixed by amount; intensity scales block displacement, rgb_shift splits channels. Animate flag defaults True. Class `GlitchEffect` in `core/components/rendering/effects/glitch_effect.py`. ObjectFx pass (per-object UV/block shift, unlike fullscreen PostFX Glitch).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `0.3` |
| Block Shift | `intensity` | FLOAT | 0.0 – 2.0 | `0.3` |
| Speed | `speed` | FLOAT | 0.0 – 20.0 | `6.0` |
| Block Size | `block_size` | FLOAT | 0.01 – 2.0 | `0.2` |
| RGB Shift | `rgb_shift` | FLOAT | 0.0 – 3.0 | `1.0` |
| Animate | `animate` | BOOL | — | `True` |

## Usage

Editor: select the mesh entity, Add Component > `GlitchEffect`, tick `enabled`, animate the amount fields from a script.

Code:

```python
from core.components.rendering.effects.glitch_effect import GlitchEffect
fx = GlitchEffect()
entity.add_component(fx)
fx.amount = 0.6
fx.intensity = 0.8
fx.animate = True
fx.enabled = True
# per-frame: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Shader snippet

Shader: `core/shaders/internal/ObjectFx.shader (u_glitch_* branch)`. Bound uniforms: `u_glitch_amount`, `u_glitch_intensity`, `u_glitch_speed`, `u_glitch_block`, `u_glitch_rgb`, `u_time`.

```glsl
// _apply uniforms (exact): u_glitch_amount, u_glitch_intensity, u_glitch_speed, u_glitch_block, u_glitch_rgb
```

## Related

Related: [Object Effects](../effects.md), ObjectFx pass (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Verified against

- `core/components/rendering/effects/glitch_effect.py` — class `GlitchEffect`, `_inspector_fields`, `__init__` defaults, `_apply` uniforms.
- `core/components/rendering/effects/object_effect.py` — `ObjectEffect` base (`bind` sets `u_time`, `_set`/`_set_vec` guards, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
