# Polygon Disintegration

> Triangle-ejection shatter. Per-cell hash (cell_size/noise_scale/stagger/thr_scale) gates ejection by amount; ejected tris fly along direction + outward normal with speed±variance, scatter/jitter, drag, gravity, spin/twist; fade shrinks alpha; surviving fringe glows with edge_width/color/emission. Optional animate/ping_pong drives amount. Class `PolygonDisintegrationEffect` in `core/components/rendering/effects/polygon_disintegration_effect.py`. ObjectFx pass with GEOMETRY-shader path: fx_geometry_shader path ejects whole triangles (see spike_growth u_spike_* geometry precedent); double_sided controls culling in _render_object_effects.

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `0.0` |
| Double Sided | `double_sided` | BOOL | — | `True` |
| Eject Direction | `direction` | VEC3 | — | `(0.0, 0.2, 0.0)` |
| Eject Speed | `speed` | FLOAT | — | `10.0` |
| Speed Variance | `sp_variance` | FLOAT | — | `4.0` |
| Outward Force | `outward` | FLOAT | — | `5.0` |
| Scatter | `scatter` | FLOAT | — | `3.0` |
| Jitter | `jitter` | FLOAT | — | `3.0` |
| Drag | `drag` | FLOAT | — | `12.0` |
| Gravity | `gravity` | FLOAT | — | `0.0` |
| Spin | `rotation` | FLOAT | — | `1.6` |
| Twist | `twist` | FLOAT | — | `1.4` |
| Fade Out | `fade` | SLIDER | 0.0 – 1.0 | `1.0` |
| Cell Size | `cell_size` | FLOAT | — | `0.08` |
| Shatter Noise | `noise_scale` | FLOAT | — | `4.6` |
| Cell Stagger | `stagger` | SLIDER | 0.0 – 1.0 | `0.595` |
| Threshold Scale | `thr_scale` | FLOAT | — | `1.45` |
| Dissolve Edge | `edge_width` | SLIDER | 0.0 – 0.5 | `0.5` |
| Edge Color | `edge_color` | COLOR | — | `[1.0, 0.55, 0.10]` |
| Edge Emission | `edge_emission` | FLOAT | — | `5.0` |
| Animate | `animate` | BOOL | — | `False` |
| Anim Speed | `speed_anim` | FLOAT | — | `0.5` |
| Ping Pong | `ping_pong` | BOOL | — | `False` |

## Usage

Editor: select the mesh entity, Add Component > `PolygonDisintegrationEffect`, tick `enabled`, animate the amount fields from a script.

Code:

```python
from core.components.rendering.effects.polygon_disintegration_effect import PolygonDisintegrationEffect
fx = PolygonDisintegrationEffect()
entity.add_component(fx)
fx.amount = 0.4
fx.speed = 8.0
fx.animate = True
fx.enabled = True
# per-frame: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Shader snippet

Shader: `core/shaders/internal/ObjectFx.shader (u_disint_* geometry + fragment branches)`. Bound uniforms: `u_disint_amount`, `u_disint_cell`, `u_disint_drag`, `u_disint_edge`, `u_disint_edge_emission`, `u_disint_fade`, `u_disint_gravity`, `u_disint_jitter`, `u_disint_noise_scale`, `u_disint_outward`, `u_disint_rot`, `u_disint_scatter`, `u_disint_sp_variance`, `u_disint_speed`, `u_disint_stagger`, `u_disint_thr_scale`, `u_disint_twist`, `u_double_sided`, `u_time`.

```glsl
// _apply uniforms (exact, 18): u_disint_amount, u_disint_cell, u_disint_speed, u_disint_sp_variance, u_disint_outward, u_disint_scatter, u_disint_jitter, u_disint_drag, u_disint_gravity, u_disint_rot, u_disint_twist, u_disint_fade, u_disint_noise_scale, u_disint_stagger, u_disint_thr_scale, u_disint_edge, u_disint_edge_emission (+ u_double_sided)
```

## Related

Related: [Object Effects](../effects.md), ObjectFx pass (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Verified against

- `core/components/rendering/effects/polygon_disintegration_effect.py` — class `PolygonDisintegrationEffect`, `_inspector_fields`, `__init__` defaults, `_apply` uniforms.
- `core/components/rendering/effects/object_effect.py` — `ObjectEffect` base (`bind` sets `u_time`, `_set`/`_set_vec` guards, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
