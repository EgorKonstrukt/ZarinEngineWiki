# Atmosphere
> Physical sky LUT: `Atmosphere` (`core/components/rendering/environment/atmosp/component.py:11-163`), singleton-per-scene via `Atmosphere.for_scene(scene)`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Computes a transmittance/multi-scatter LUT (`atmosp/compute.py`, `AtmosphereLutMixin`); `_resolution_scale` scales the LUT. Only the first enabled instance per scene is used (`for_scene`). All fields are underscore-prefixed in serialization.
## Properties
| name | type | default | description |
|---|---|---|---|
| `_intensity` | slider (1–60) | `20.0` | Sky gain (`component.py:20,42`). |
| `_sun_intensity` | slider (0–10) | `1.0` | Sun disk gain (`component.py:22,43`). |
| `_resolution_scale` | slider (0.25–1) | `1.0` | LUT resolution (`component.py:23,44`). |
| `_ozone_factor` | slider (0–3) | `1.0` | Ozone absorption (`component.py:24,45`). |
| `_aerosol_scale` | slider (0–5) | `1.0` | Aerosols/Mie amount (`component.py:25,46`). |
| `_rayleigh_scale` | slider (0–3) | `1.0` | Rayleigh amount (`component.py:26,47`). |
| `_mie_anisotropy` | slider (0–0.95) | `0.76` | Mie g (`component.py:27,48`). |
| `_mie_albedo` | slider (0–1) | `0.9` | Mie albedo (`component.py:28,49`). |
| `_ground_albedo` | slider (0–1) | `0.3` | Ground bounce (`component.py:29,50`). |
| `_multiscatter_strength` | slider (0–2) | `1.0` | Multi-scatter gain (`component.py:30,51`). |
| `_horizon_haze` | slider (0–2) | `0.4` | Horizon haze (`component.py:31,52`). |
| `_planet_radius` | slider km (6000–6500) | `6360.0` | Planet radius (`component.py:32,53`). |
| `_atmosphere_height` | slider km (20–120) | `60.0` | Top height (`component.py:33,54`). |
| `_camera_height_m` | slider m (0–8000) | `20.0` | Reference eye height (`component.py:34,55`). |
| `_sun_angular_radius` | slider deg (0.05–1) | `0.27` | Sun size (`component.py:35,56`). |
| `_sun_limb_darkening` | slider (0–1) | `0.7` | Limb darkening (`component.py:36,57`). |
| `_sun_convergence` | slider (0–1) | `0.5` | Edge softness (`component.py:37,58`). |
| `_color_temperature` | slider K (2000–20000) | `5778.0` | Sun blackbody (`component.py:38,59`). |
## Usage in Editor
Single Atmosphere per scene. Lower `_rayleigh_scale` for alien skies, raise `_aerosol_scale`/`_horizon_haze` for dusty sunsets.
## Usage in Code
```python
atmo = Atmosphere.for_scene(scene)
atmo._sun_intensity = 1.4
atmo._rayleigh_scale = 0.8
```
## Related pages
- [Procedural Sky](./procedural-sky.md), [Skybox](./skybox.md), [Directional Light](./directional-light.md)
## Verified against
- `core/components/rendering/environment/atmosp/component.py:16-60,84-163`
