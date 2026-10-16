# Wind Sway

> Generic vertex sway. Normalized direction × strength × amount offsets vertices with phase from u_time × speed; fast-path writes u_wind_amount = 0 when disabled or amount ≤ 0. Class `WindSwayEffect` in `core/components/rendering/effects/wind_sway_effect.py`. ObjectFx pass vertex bend (generic props/grass), same collection/bind path as Dissolve.

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `1.0` |
| Wind Direction | `direction` | VEC3 | -1.0 – 1.0 | `(1.0, 0.0, 0.0)` |
| Speed | `speed` | FLOAT | 0.0 – 20.0 | `2.0` |
| Sway | `strength` | FLOAT | 0.0 – 3.0 | `0.3` |

## Usage

Editor: select the mesh entity, Add Component > `WindSwayEffect`, tick `enabled`, animate the amount fields from a script.

Code:

```python
from core.components.rendering.effects.wind_sway_effect import WindSwayEffect
fx = WindSwayEffect()
entity.add_component(fx)
fx.amount = 1.0
fx.strength = 0.5
fx.speed = 3.0
fx.enabled = True
# per-frame: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Shader snippet

Shader: `core/shaders/internal/ObjectFx.shader (u_wind_* branch)`. Bound uniforms: `u_wind_amount`, `u_wind_dir`, `u_wind_speed`, `u_wind_strength`, `u_time`.

```glsl
// wind_sway_effect.py::_apply (exact):
// prog["u_wind_amount"] = float(amount)  // 0 fast-path when disabled
// prog["u_wind_dir"] = normalized vec3, prog["u_wind_speed"], prog["u_wind_strength"]
```

## Related

Related: [Object Effects](../effects.md), ObjectFx pass (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Verified against

- `core/components/rendering/effects/wind_sway_effect.py` — class `WindSwayEffect`, `_inspector_fields`, `__init__` defaults, `_apply` uniforms.
- `core/components/rendering/effects/object_effect.py` — `ObjectEffect` base (`bind` sets `u_time`, `_set`/`_set_vec` guards, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
