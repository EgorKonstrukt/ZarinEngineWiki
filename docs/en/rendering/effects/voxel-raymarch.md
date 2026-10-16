# Voxel Raymarch

> Mesh→volume raymarch. Host mesh is voxelized on CPU into a 3D texture (dims clamped to 192 max), then a fullscreen pass raymarches (max_steps) with Beer-Lambert-ish alpha × opacity × amount, shaded by color/emission/rim and light_dir. Blends with SRC_ALPHA/ONE_MINUS_SRC_ALPHA over the scene color target. Class `VoxelRaymarchEffect` in `core/components/rendering/effects/voxelp/effect.py`. NOTE: despite living under effects/, this is a GraphicsEffect (fullscreen raymarch into a 3D occupancy texture), not an ObjectEffect. Requires a MeshFilter on the same entity to build the grid; disabled/fast-pathed when amount/opacity ≤ 0.001.

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `1.0` |
| Voxel Size | `voxel_size` | FLOAT | — | `0.3` |
| Voxel Color | `color` | COLOR | — | `[0.4, 1.0, 0.6]` |
| Glow | `emission` | FLOAT | — | `0.6` |
| Rim Power | `rim` | FLOAT | — | `2.5` |
| Opacity | `opacity` | SLIDER | 0.0 – 1.0 | `1.0` |
| Max Steps | `max_steps` | INT | 32 – 1024 | `256` |
| Light Dir | `light_dir` | VEC3 | — | `(0.4, 0.8, 0.3)` |

## Usage

Editor: select the mesh entity, Add Component > `VoxelRaymarchEffect`, tick `enabled`, animate the amount fields from a script.

Code:

```python
from core.components.rendering.effects.voxelp.effect import VoxelRaymarchEffect
fx = VoxelRaymarchEffect()
entity.add_component(fx)
fx.voxel_size = 0.2
fx.max_steps = 128
fx.opacity = 0.9
fx.enabled = True
# per-frame: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Shader snippet

Shader: `core/components/rendering/effects/voxelp/shaders.py (VR_VERT / VR_FRAG)`. Bound uniforms: `u_inv_view_proj`, `u_cam_pos`, `u_grid_min`, `u_grid_dims`, `u_cell_size`, `u_vox_color`, `u_vox_emission`, `u_vox_rim`, `u_amount`, `u_opacity`, `u_light_dir`, `u_max_steps`, `u_grid`, `u_depth_tex`.

```glsl
// VR_FRAG uniforms (exact names from shaders.py):
// uniform sampler3D u_grid; uniform vec3 u_grid_min; uniform vec3 u_grid_dims; uniform float u_cell_size;
// uniform mat4 u_inv_view_proj; uniform vec3 u_cam_pos; uniform sampler2D u_depth_tex;
// uniform vec3 u_vox_color; uniform float u_vox_emission; uniform float u_vox_rim;
// uniform float u_amount; uniform float u_opacity; uniform vec3 u_light_dir; uniform int u_max_steps;
```

## Related

Related: [Object Effects](../effects.md), [Voxelize](voxelize.md), `core/components/rendering/effects/voxelp/shaders.py`, `core/components/rendering/effects/voxel_cpu.py`.

## Verified against

- `core/components/rendering/effects/voxelp/effect.py` — class `VoxelRaymarchEffect`, `_inspector_fields`, `__init__` defaults, `_apply` uniforms.
- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `_intensity_prop = "amount"`, `render()` signature).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
