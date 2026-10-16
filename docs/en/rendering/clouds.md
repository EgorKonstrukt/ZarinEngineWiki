# Clouds
> Volumetric cloud layer: `Cloud` (`core/components/rendering/environment/clouds.py:17-273`), shader `core/shaders/materials/Clouds.shader` (+ `CloudLayer.shader` plane fallback).
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Full-screen raymarch (`render_clouds`) uses `u_time`, `u_cam_pos`, `u_inv_view_proj`, `_Coverage…_Tint` uniforms and the sun dir/color from the [Directional Light](./directional-light.md) (`clouds.py:75-150`). Planar fallback (`render_cloud_layer`) skips when `height < 8.0` or `opacity <= 0.001` and draws a `1400` m plane at `height + thickness*0.5` following the camera (`clouds.py:152-217`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `material_path` | shader path | `core/shaders/materials/Clouds.shader` | Cloud shader (`clouds.py:24,52`). |
| `coverage` | slider (0–1) | `0.78` | Cloud cover (`clouds.py:25,53`). |
| `density` | slider (0–2) | `1.12` | Optical density (`clouds.py:26,54`). |
| `speed` | float (0–5) | `0.38` | Wind speed (`clouds.py:27,55`). |
| `wind_direction` | float deg (0–360) | `18.0` | Wind heading (`clouds.py:28,56`). |
| `height` | float (0–500) | `82.0` | Layer base, m (`clouds.py:29,57`). |
| `thickness` | float (0–200) | `42.0` | Layer depth, m (`clouds.py:30,58`). |
| `scale` | float (0.001–1) | `0.014` | Noise scale (`clouds.py:31,59`). |
| `opacity` | slider (0–1) | `0.68` | Alpha (`clouds.py:32,60`). |
| `shadow_strength` | slider (0–1) | `0.52` | Self-shadow (`clouds.py:33,61`). |
| `fog_density` | slider (0–1) | `0.0` | In-cloud fog (`clouds.py:34,62`). |
| `height_falloff` | float (0–4) | `0.025` | Vertical fade (`clouds.py:35,63`). |
| `scattering` | slider (0–2) | `1.05` | Forward scatter (`clouds.py:36,64`). |
| `absorption` | slider (0–4) | `0.92` | Beer absorption (`clouds.py:37,65`). |
| `softness` | slider (0.02–0.8) | `0.28` | Edge softness (`clouds.py:38,66`). |
| `detail_strength` | slider (0–1) | `0.92` | Erosion detail (`clouds.py:39,67`). |
| `steps` | int slider (4–32) | `16` | March steps (`clouds.py:40,68`). |
| `evolution` | slider (0–2) | `1.1` | Time morph (`clouds.py:41,69`). |
| `contrast` | slider (0.2–2.5) | `1.7` | Density contrast (`clouds.py:42,70`). |
| `silver_lining` | slider (0–2) | `1.35` | Edge brightening (`clouds.py:43,71`). |
| `seed` | float (0–10000) | `11.3` | Noise seed (`clouds.py:44,72`). |
| `tint` | color3 | `[0.86, 0.92, 1.0]` | Tint (`clouds.py:45,73`). |
## Usage in Editor
Raise Coverage/Density for overcast, Steps for quality (cost), Silver Lining for backlit edges. Keep Height ≥ 8 for the plane fallback.
## Usage in Code
```python
cl.coverage = 0.5
cl.steps = 24
cl.wind_direction = 45.0
```
## Related pages
- [Procedural Sky](./procedural-sky.md), [Directional Light](./directional-light.md), [Atmosphere](./atmosphere.md)
## Verified against
- `core/components/rendering/environment/clouds.py:22-73,75-217`
