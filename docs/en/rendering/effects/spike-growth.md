# Spike Growth

> Geometry-shader spikes. Each triangle extrudes up to 4× (max 12 verts) along normals; length × amount sets height, radius shrinks the base, noise/cell vary per-spike length, twist rotates, emission/color + rim shade the spikes. Optional animate/ping_pong. Class `SpikeGrowthEffect` in `core/components/rendering/effects/spike_growth_effect.py`. ObjectFx geometry-shader extrusion (layout triangles → triangle_strip max_vertices = 12): vertices push along normals by length × amount with cell noise.

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `0.5` |
| Spike Length | `length` | FLOAT | — | `1.5` |
| Base Shrink | `radius` | FLOAT | — | `0.35` |
| Length Noise | `noise` | FLOAT | — | `0.6` |
| Twist | `twist` | FLOAT | — | `0.6` |
| Glow | `emission` | FLOAT | — | `3.0` |
| Spike Color | `color` | COLOR | — | `[0.35, 0.9, 1.0]` |
| Rim Power | `rim` | FLOAT | — | `2.5` |
| Cell Size | `cell` | FLOAT | — | `0.25` |
| Double Sided | `double_sided` | BOOL | — | `True` |
| Animate | `animate` | BOOL | — | `False` |
| Anim Speed | `speed_anim` | FLOAT | — | `0.5` |
| Ping Pong | `ping_pong` | BOOL | — | `False` |

## Usage

Editor: select the mesh entity, Add Component > `SpikeGrowthEffect`, tick `enabled`, animate the amount fields from a script.

Code:

```python
from core.components.rendering.effects.spike_growth_effect import SpikeGrowthEffect
fx = SpikeGrowthEffect()
entity.add_component(fx)
fx.amount = 0.7
fx.length = 2.0
fx.enabled = True
# per-frame: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Shader snippet

Shader: `core/components/rendering/effects/spike_growth_effect.py (geometry header) + ObjectFx.shader`. Bound uniforms: `u_spike_amount`, `u_spike_length`, `u_spike_radius`, `u_spike_noise`, `u_spike_twist`, `u_spike_emission`, `u_spike_color`, `u_spike_rim`, `u_spike_cell`, `u_double_sided`, `u_time`.

```glsl
#version 330 core
layout(triangles) in;
layout(triangle_strip, max_vertices = 12) out;
// uniforms: u_spike_amount, u_spike_length, u_spike_radius, u_spike_noise, u_spike_twist, u_spike_cell, u_spike_color, u_spike_emission, u_spike_rim
```

## Related

Related: [Object Effects](../effects.md), ObjectFx pass (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Verified against

- `core/components/rendering/effects/spike_growth_effect.py` — class `SpikeGrowthEffect`, `_inspector_fields`, `__init__` defaults, `_apply` uniforms.
- `core/components/rendering/effects/object_effect.py` — `ObjectEffect` base (`bind` sets `u_time`, `_set`/`_set_vec` guards, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
