# Frost

> Directional ice coverage. Freeze gradient along direction mixes ice color by coverage × amount; crack pattern scale sets fracture frequency; sparkle adds view glints; rim_power shapes the fresnel rim. Optional animate flag. Class `FrostEffect` in `core/components/rendering/effects/frost_effect.py`. ObjectFx pass, same collection/bind path as Dissolve.

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `1.0` |
| Ice Color | `color` | COLOR | — | `[0.8, 0.92, 1.0]` |
| Coverage | `coverage` | SLIDER | 0.0 – 1.0 | `0.7` |
| Crack Scale | `crack_scale` | FLOAT | 0.1 – 20.0 | `3.0` |
| Sparkle | `sparkle` | SLIDER | 0.0 – 2.0 | `0.6` |
| Rim Power | `rim_power` | FLOAT | 0.5 – 8.0 | `3.0` |
| Freeze Direction | `direction` | VEC3 | -1.0 – 1.0 | `(0.0, 1.0, 0.0)` |
| Animate | `animate` | BOOL | — | `False` |

## Usage

Editor: select the mesh entity, Add Component > `FrostEffect`, tick `enabled`, animate the amount fields from a script.

Code:

```python
from core.components.rendering.effects.frost_effect import FrostEffect
fx = FrostEffect()
entity.add_component(fx)
fx.amount = 0.8
fx.coverage = 0.9
fx.enabled = True
# per-frame: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Shader snippet

Shader: `core/shaders/internal/ObjectFx.shader (u_frost_* branch)`. Bound uniforms: `u_frost_amount`, `u_frost_coverage`, `u_frost_crack`, `u_frost_sparkle`, `u_frost_rim`, `u_frost_animate`, `u_time`.

```glsl
// _apply uniforms (exact): u_frost_amount, u_frost_coverage, u_frost_crack, u_frost_sparkle, u_frost_rim, u_frost_animate
```

## Related

Related: [Object Effects](../effects.md), ObjectFx pass (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Verified against

- `core/components/rendering/effects/frost_effect.py` — class `FrostEffect`, `_inspector_fields`, `__init__` defaults, `_apply` uniforms.
- `core/components/rendering/effects/object_effect.py` — `ObjectEffect` base (`bind` sets `u_time`, `_set`/`_set_vec` guards, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
