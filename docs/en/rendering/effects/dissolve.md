# Dissolve

> Noise-gradient burn-off. Fragments past amount (biased by directional gradient × noise_strength + noise field × noise_scale) are discarded; a band of width edge_width glows with edge_color × edge_emission. Optional time animation drives amount 0→1 (ping-pong or loop). Class `DissolveEffect` in `core/components/rendering/effects/dissolve_effect.py`. ObjectFx pass (`core/renderer/object_fx_pass.py`): enabled DissolveEffect components are collected per entity (`_find_object_effect`) and bound in the FX program before the mesh draw.

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `0.0` |
| Noise Strength | `noise_strength` | SLIDER | 0.0 – 1.0 | `1.0` |
| Noise Scale | `noise_scale` | FLOAT | 0.1 – 20.0 | `4.0` |
| Gradient Direction | `direction` | VEC3 | -1.0 – 1.0 | `(0.0, 1.0, 0.0)` |
| Invert | `invert` | BOOL | — | `False` |
| Edge Width | `edge_width` | SLIDER | 0.0 – 0.5 | `0.05` |
| Edge Color | `edge_color` | COLOR | — | `[1.0, 0.4, 0.05]` |
| Edge Emission | `edge_emission` | SLIDER | 0.0 – 5.0 | `2.5` |
| Animate | `animate` | BOOL | — | `False` |
| Speed | `speed` | FLOAT | 0.0 – 5.0 | `0.5` |
| Ping Pong | `ping_pong` | BOOL | — | `False` |

## Usage

Editor: select the mesh entity, Add Component > `DissolveEffect`, tick `enabled`, animate the amount fields from a script.

Code:

```python
from core.components.rendering.effects.dissolve_effect import DissolveEffect
fx = DissolveEffect()
entity.add_component(fx)
fx.amount = 0.5
fx.edge_width = 0.08
fx.animate = True
fx.enabled = True
# per-frame: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Shader snippet

Shader: `core/shaders/internal/ObjectFx.shader (u_dissolve_* branch) + dissolve_effect.py::_apply`. Bound uniforms: `u_dissolve_amount`, `u_dissolve_edge`, `u_dissolve_color`, `u_dissolve_edge_emission`, `u_dissolve_noise_scale`, `u_dissolve_noise_strength`, `u_dissolve_dir`, `u_dissolve_invert`, `u_time`.

```glsl
// dissolve_effect.py::_apply (exact uniform names):
// prog["u_dissolve_amount"] = float(amount)
// prog["u_dissolve_edge"] = float(edge_width)
// prog["u_dissolve_color"] = vec3(edge_color)
// prog["u_dissolve_edge_emission"], prog["u_dissolve_noise_scale"], prog["u_dissolve_noise_strength"], prog["u_dissolve_dir"], prog["u_dissolve_invert"]
```

## Related

Related: [Object Effects](../effects.md), ObjectFx pass (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Verified against

- `core/components/rendering/effects/dissolve_effect.py` — class `DissolveEffect`, `_inspector_fields`, `__init__` defaults, `_apply` uniforms.
- `core/components/rendering/effects/object_effect.py` — `ObjectEffect` base (`bind` sets `u_time`, `_set`/`_set_vec` guards, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
