# Voxelize

> Mesh→cubes breakup. CPU voxelizer (voxel_cpu.compute_voxel_instances) bins the mesh into voxel_size grid (world_grid toggles world vs local snap, jitter offsets); cubes shaded with color/emission/rim; amount cross-fades; cell is the shading cell; optional animate/ping_pong. Class `VoxelizeEffect` in `core/components/rendering/effects/voxelize_effect.py`. ObjectFx pass + instanced cubes path: _render_voxel_instances() in object_fx_pass.py draws voxel cubes when enabled and amount > 0; show_base_mesh toggles the original mesh draw.

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `0.5` |
| Show Base Mesh | `show_base_mesh` | BOOL | — | `True` |
| World Grid | `world_grid` | BOOL | — | `False` |
| Voxel Size | `voxel_size` | FLOAT | — | `0.3` |
| Voxel Scale XYZ | `scale` | VEC3 | — | `(1.0, 1.0, 1.0)` |
| Jitter | `jitter` | FLOAT | — | `0.0` |
| Glow | `emission` | FLOAT | — | `2.5` |
| Voxel Color | `color` | COLOR | — | `[0.4, 1.0, 0.6]` |
| Rim Power | `rim` | FLOAT | — | `2.5` |
| Cell Size | `cell` | FLOAT | — | `0.3` |
| Double Sided | `double_sided` | BOOL | — | `True` |
| Animate | `animate` | BOOL | — | `False` |
| Anim Speed | `speed_anim` | FLOAT | — | `0.5` |
| Ping Pong | `ping_pong` | BOOL | — | `False` |

## Usage

Editor: select the mesh entity, Add Component > `VoxelizeEffect`, tick `enabled`, animate the amount fields from a script.

Code:

```python
from core.components.rendering.effects.voxelize_effect import VoxelizeEffect
fx = VoxelizeEffect()
entity.add_component(fx)
fx.amount = 0.7
fx.voxel_size = 0.25
fx.show_base_mesh = False
fx.enabled = True
# per-frame: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Shader snippet

Shader: `core/components/rendering/effects/voxel_cpu.py + core/shaders/legacy/voxelize.comp + ObjectFx.shader`. Bound uniforms: `u_vox_amount (+ voxel instance buffers)`, `u_time`.

```glsl
// object_fx_pass.py: show_base = (vox is None) or (not vox.enabled) or vox.show_base_mesh
// if vox.enabled and vox.amount > 0: _render_voxel_instances(entry, vox, ...)
```

## Related

Related: [Object Effects](../effects.md), ObjectFx pass (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Verified against

- `core/components/rendering/effects/voxelize_effect.py` — class `VoxelizeEffect`, `_inspector_fields`, `__init__` defaults, `_apply` uniforms.
- `core/components/rendering/effects/object_effect.py` — `ObjectEffect` base (`bind` sets `u_time`, `_set`/`_set_vec` guards, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
