# Hologram

> Scanline hologram. Base color tinted by color × amount; horizontal scan bands (density/speed) + flicker modulate alpha; rim_power adds fresnel edge. Class `HologramEffect` in `core/components/rendering/effects/hologram_effect.py`. ObjectFx pass, additive-ish scanline/fresnel composite.

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `1.0` |
| Color | `color` | COLOR | — | `[0.2, 0.9, 1.0]` |
| Scan Density | `scan_density` | FLOAT | 1.0 – 200.0 | `40.0` |
| Scan Speed | `scan_speed` | FLOAT | 0.0 – 20.0 | `3.0` |
| Flicker | `flicker` | SLIDER | 0.0 – 1.0 | `0.15` |
| Rim Power | `rim_power` | FLOAT | 0.5 – 8.0 | `2.0` |

## Usage

Editor: select the mesh entity, Add Component > `HologramEffect`, tick `enabled`, animate the amount fields from a script.

Code:

```python
from core.components.rendering.effects.hologram_effect import HologramEffect
fx = HologramEffect()
entity.add_component(fx)
fx.amount = 1.0
fx.scan_density = 60.0
fx.enabled = True
# per-frame: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Shader snippet

Shader: `core/shaders/internal/ObjectFx.shader (u_holo_* branch)`. Bound uniforms: `u_holo_amount`, `u_holo_scan`, `u_holo_speed`, `u_holo_flicker`, `u_holo_rim`, `(+ holo color)`, `u_time`.

```glsl
// _apply uniforms (exact): u_holo_amount, u_holo_scan, u_holo_speed, u_holo_flicker, u_holo_rim
```

## Related

Related: [Object Effects](../effects.md), ObjectFx pass (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Verified against

- `core/components/rendering/effects/hologram_effect.py` — class `HologramEffect`, `_inspector_fields`, `__init__` defaults, `_apply` uniforms.
- `core/components/rendering/effects/object_effect.py` — `ObjectEffect` base (`bind` sets `u_time`, `_set`/`_set_vec` guards, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
