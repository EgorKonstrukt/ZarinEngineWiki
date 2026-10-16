# Emissive Pulse

> Sinusoidal emissive lift. Emission color × strength × (0.5 + 0.5·sin(u_time·speed)) is added, gated by amount. Class `EmissivePulseEffect` in `core/components/rendering/effects/emissive_pulse_effect.py`. ObjectFx pass, same collection/bind path as Dissolve.

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `1.0` |
| Pulse Color | `color` | COLOR | — | `[1.0, 0.4, 0.1]` |
| Speed | `speed` | FLOAT | 0.0 – 20.0 | `3.0` |
| Intensity | `strength` | FLOAT | 0.0 – 10.0 | `2.0` |

## Usage

Editor: select the mesh entity, Add Component > `EmissivePulseEffect`, tick `enabled`, animate the amount fields from a script.

Code:

```python
from core.components.rendering.effects.emissive_pulse_effect import EmissivePulseEffect
fx = EmissivePulseEffect()
entity.add_component(fx)
fx.amount = 1.0
fx.strength = 3.0
fx.speed = 4.0
fx.enabled = True
# per-frame: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Shader snippet

Shader: `core/shaders/internal/ObjectFx.shader (u_pulse_* branch)`. Bound uniforms: `u_pulse_amount`, `u_pulse_speed`, `u_pulse_strength`, `(+ color buffer)`, `u_time`.

```glsl
// _apply uniforms (exact): u_pulse_amount, u_pulse_speed, u_pulse_strength (+ pulse color vec3)
```

## Related

Related: [Object Effects](../effects.md), ObjectFx pass (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Verified against

- `core/components/rendering/effects/emissive_pulse_effect.py` — class `EmissivePulseEffect`, `_inspector_fields`, `__init__` defaults, `_apply` uniforms.
- `core/components/rendering/effects/object_effect.py` — `ObjectEffect` base (`bind` sets `u_time`, `_set`/`_set_vec` guards, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
