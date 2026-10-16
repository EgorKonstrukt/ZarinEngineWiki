# Voxel
> Voxelized look: `VoxelizeEffect` object effect (`core/components/rendering/effects/voxelize_effect.py:17-152`) rendered by `VoxelPassMixin` (`core/renderer/voxel_pass.py`), compute `core/shaders/legacy/voxelize.comp`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
CPU fallback `compute_voxel_instances` (`effects/voxel_cpu.py:50`); GPU path via `voxelize.comp` when available, else per-mesh CPU cells (`voxel_pass.py:218-221`). Caps: `_VOX_MAX_DIM = 128`, `_VOX_MAX_INSTANCES = 2200000` (`voxel_pass.py:22-25`). `amount` blends base→voxels; `animate` (+`ping_pong`) drives `amount` as `t % 1` triangle (`voxelize_effect.py:78-86`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `amount` | slider (0–1) | `0.5` | Voxelization blend (`voxelize_effect.py:138,36`). |
| `show_base_mesh` | bool | `True` | Keep base mesh (`voxelize_effect.py:139,37`). |
| `world_grid` | bool | `False` | Snap cells to world grid (`voxelize_effect.py:140,38`). |
| `voxel_size` | float | `0.3` | Cell size (`voxelize_effect.py:141,39`). |
| `scale` | vec3 | `[1, 1, 1]` | Per-voxel XYZ scale (`voxelize_effect.py:142,40`). |
| `jitter` | float | `0.0` | Cell jitter (`voxelize_effect.py:143,41`). |
| `emission` | float | `2.5` | Glow term (`voxelize_effect.py:144,42`). |
| `color` | color3 | `[0.4, 1.0, 0.6]` | Voxel color (`voxelize_effect.py:145,43`). |
| `rim` | float | `2.5` | Fresnel power (`voxelize_effect.py:146,44`). |
| `cell` | float | `0.3` | Cell render size (`voxelize_effect.py:147,45`). |
| `double_sided` | bool | `True` | Disable culling (`voxelize_effect.py:148,46`). |
| `animate` | bool | `False` | Animate amount (`voxelize_effect.py:149,47`). |
| `speed_anim` | float | `0.5` | Anim rate (`voxelize_effect.py:150,48`). |
| `ping_pong` | bool | `False` | Triangle vs saw (`voxelize_effect.py:151,49`). |
## Usage in Editor
Add Voxelize Effect to a mesh entity, raise Voxel Size for chunkier cubes, enable World Grid for Minecraft-style alignment across objects.
## Usage in Code
```python
vx.voxel_size = 0.2
vx.amount = 1.0
vx.animate = True
vx.ping_pong = True
```
## Related pages
- [Mesh Renderer](./mesh-renderer.md), [Pipeline](./pipeline.md)
## Verified against
- `core/components/rendering/effects/voxelize_effect.py:34-152`
- `core/renderer/voxel_pass.py:22-63,199-258`
- `core/shaders/legacy/voxelize.comp`
