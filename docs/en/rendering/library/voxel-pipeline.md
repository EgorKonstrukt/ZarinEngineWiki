# Voxel Pipeline

> Object voxelization (`VoxelizeEffect`), the `voxel_pass` instancing backend with its hard caps, and the screen-space `VoxelRaymarchEffect`.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Two components cover voxels: `VoxelizeEffect` (an `ObjectEffect` that voxelizes the host mesh) and `VoxelRaymarchEffect` (a `GraphicsEffect` that raymarches a 3D occupancy texture). Instanced voxel cubes are drawn by `VoxelPassMixin` (`core/renderer/voxel_pass.py:18-25`).

## VoxelizeEffect (`core/components/rendering/effects/voxelize_effect.py:18-52`)

| name | type | default | description |
|---|---|---|---|
| `amount` | slider 0–1 | `0.5` | Blend between base mesh and voxels. |
| `show_base_mesh` | bool | `True` | Keep drawing the original mesh. |
| `world_grid` | bool | `False` | Snap voxels to a world grid instead of object space. |
| `voxel_size` | float | `0.3` | Target voxel size; also `cell = 0.3`. |
| `scale` | vec3 | `[1,1,1]` | Per-voxel cube scale. |
| `jitter` | float | `0.0` | Cell jitter seed factor. |
| `emission` | float | `2.5` | Voxel glow. |
| `color` | color3 | `[0.4, 1.0, 0.6]` | Voxel tint. |
| `rim` | float | `2.5` | Rim power. |
| `double_sided` | bool | `True` | Two-sided voxel cubes. |
| `animate` / `speed_anim` / `ping_pong` | bool / float / bool | `False` / `0.5` / `False` | Auto-animate `amount` 0→1. |

Voxel cells are computed on CPU by `compute_voxel_instances` and cached per (mesh, size, grid, jitter) key (`voxelize_effect.py:59-72`).

## voxel_pass caps (`core/renderer/voxel_pass.py:22-25`)

| constant | value | meaning |
|---|---|---|
| `_VOX_MAX_DIM` | `128` | Max grid dimension; larger extents rescale the cell (`voxel_pass.py:304-309`). |
| `_VOX_MAX_INSTANCES` | `2200000` | Instance clamp: `need_inst = min(total, _VOX_MAX_INSTANCES)` (`voxel_pass.py:164`). |

The instanced vertex shader (`_VOXEL_INST_VERT`, `#version 330 core`) packs `i_cell.xyz` (center) + `i_cell.w` (cull flag) with `u_vox_size`/`u_vox_scale`.

## VoxelRaymarchEffect (`core/components/rendering/effects/voxelp/effect.py:22-59`)

A `GraphicsEffect` (`render_type = "additive"`, `_intensity_prop = "amount"`, multi-instance allowed) rendering a fullscreen raymarch over a `texture3d` occupancy grid built from the host `MeshFilter`.

| name | type | default | description |
|---|---|---|---|
| `amount` | slider 0–1 | `1.0` | Effect intensity (auto-skipped at `<= 0.001`). |
| `voxel_size` | float | `0.3` | Grid cell size. |
| `color` | color3 | `[0.4, 1.0, 0.6]` | Voxel color. |
| `emission` | float | `0.6` | Glow. |
| `rim` | float | `2.5` | Rim power. |
| `opacity` | slider 0–1 | `1.0` | Output opacity. |
| `max_steps` | int 32–1024 | `256` | Raymarch steps. |
| `light_dir` | vec3 | `[0.4, 0.8, 0.3]` | March lighting direction. |

Grid build cap: `max_dim = 192` — if `ceil(extent / cell)` exceeds it, the cell is enlarged to `max(extent) / 192` (`effect.py:164-167`).

## Usage in Code

```python
from core.components.rendering.effects.voxelize_effect import VoxelizeEffect
vx = VoxelizeEffect()
vx.amount = 0.5
vx.voxel_size = 0.3
entity.add_component(vx)
```

## Related pages

- [Voxelize](../effects/voxelize.md), [Voxel Raymarch](../effects/voxel-raymarch.md), [Voxel Rendering](../voxel.md), [Object Effects](../effects.md)

## Verified against

- `core/components/rendering/effects/voxelize_effect.py:18-152` (defaults, serializer, inspector)
- `core/renderer/voxel_pass.py:18-25,164,304-309` (`_VOX_MAX_DIM = 128`, `_VOX_MAX_INSTANCES = 2200000`)
- `core/components/rendering/effects/voxelp/effect.py:22-91,156-185` (`VoxelRaymarchEffect`, `max_dim = 192`, `max_steps = 256`)
- `core/renderer/object_fx_pass.py:15,114` (voxel effect hook)
