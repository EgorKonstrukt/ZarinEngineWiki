# Particle Force Field
> Particle influencer: `ParticleForceField` (`core/components/rendering/particles/particle_force_field.py`); max `16` fields uploaded as SSBO (`MAX_FORCE_FIELDS = 16`).
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`ForceFieldShape`: `sphere / box` (`particle_force_field.py:29-31`). `to_gpu_data()` packs position+radius, force+intensity, half-extents+shape flag, extras (start_range/drag/gravity/distance flag) (`particle_force_field.py:75-103`). Gizmo draws sphere rings or box edges + inner start-range cage (`particle_force_field.py:105-168`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `shape` | `ForceFieldShape` | `SPHERE` | Influence volume (`particle_force_field.py:46,62`). |
| `radius` | float (0.01–500) | `5.0` | Sphere radius (`particle_force_field.py:47,63`). |
| `box_size` | vec3 (0.01–500) | `[5.0, 5.0, 5.0]` | Box extents (`particle_force_field.py:48,64`). |
| `start_range` | float (0–500) | `0.0` | Inner dead zone (`particle_force_field.py:49,65`). |
| `force_x` / `force_y` / `force_z` | float (−1000…1000) | `0.0` / `1.0` / `0.0` | Force vector (`particle_force_field.py:51-53,66-68`). |
| `intensity` | float (0–100) | `1.0` | Force scale (`particle_force_field.py:54,69`). |
| `multiply_by_distance` | bool | `True` | Scale by distance (`particle_force_field.py:55,70`). |
| `drag` | float (0–100) | `0.0` | Damping (`particle_force_field.py:56,71`). |
| `gravity` | float (−100…100) | `0.0` | Extra gravity (`particle_force_field.py:57,72`). |
## Usage in Editor
One per entity (`_allow_multiple = False`). Sphere for explosions/updrafts, Box for wind tunnels; Start Range hollows the core.
## Usage in Code
```python
ff.shape = ForceFieldShape.BOX
ff.box_size = [10.0, 4.0, 10.0]
ff.force_y = 3.0
ff.drag = 0.2
```
## Related pages
- [Particle System](./particle-system.md)
## Verified against
- `core/components/rendering/particles/particle_force_field.py:17-103,170-202`
