# Water
> Water surface: `Water` (`core/components/rendering/environment/waterp/component.py:26-236`), shader `core/shaders/materials/Water.shader`, drawn by `core/renderer/water_pass.py`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`surface_type`: `Ocean` (infinite, `ocean_size`) or `Pond` (`pond_size`). Eight default Gerstner-ish `waves[i] = {direction, amplitude, wavelength, speed, steepness}` (`component.py:14-23`). Interaction + sim blocks drive shore ripples (`sim_damping / sim_propagation / sim_saturation / sim_disp_scale / sim_normal_scale`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `material_path` | shader path | `core/shaders/materials/Water.shader` | Water shader (`component.py:34,92`). |
| `surface_type` | enum `Ocean/Pond` | `Ocean` | Ocean vs pond (`component.py:35,93`). |
| `infinite_ocean` | bool | `True` | Camera-following plane (`component.py:36,94`). |
| `ocean_size` | float (10–5000) | `2000.0` | Ocean extent (`component.py:37,95`). |
| `waves` | list of 8×{direction, amplitude, wavelength, speed, steepness} | see code | Wave train (`component.py:38-44,96`). |
| `deep_color` | color3 | `[0.02, 0.18, 0.28]` | Deep albedo (`component.py:45,97`). |
| `shallow_color` | color3 | `[0.10, 0.42, 0.52]` | Shallow albedo (`component.py:46,98`). |
| `foam_color` | color3 | `[0.92, 0.97, 1.0]` | Foam (`component.py:47,99`). |
| `sss_color` | color3 | `[0.0, 0.55, 0.45]` | Subsurface (`component.py:48,100`). |
| `horizon_color` | color3 | `[0.62, 0.78, 0.86]` | Distance tint (`component.py:49,101`). |
| `smoothness` | slider (0–1) | `0.96` | Specular sharpness (`component.py:50,102`). |
| `distortion` | slider (0–0.3) | `0.04` | Refraction wobble (`component.py:51,103`). |
| `normal_strength` | slider (0–1) | `0.35` | Detail normals (`component.py:52,104`). |
| `wave_tiling` | slider (0.01–2) | `0.25` | Wave UV tiling (`component.py:53,105`). |
| `warp_amount` | slider (0–4) | `1.6` | Domain warp (`component.py:54,106`). |
| `detail_speed` | slider (0–3) | `1.0` | Detail scroll speed (`component.py:56,107`). |
| `refract_strength` | slider (0–1) | `0.55` | Refraction tint (`component.py:57,108`). |
| `fresnel_power` | slider (0.5–8) | `4.0` | Fresnel exponent (`component.py:58,109`). |
| `foam_strength` | slider (0–3) | `1.0` | Foam gain (`component.py:59,110`). |
| `specular` | slider (0–4) | `1.0` | Sun glint (`component.py:60,111`). |
| `shore_fade` | float (0.1–20) | `3.0` | Shore fade, m (`component.py:61,112`). |
| `choppiness` | slider (0–2) | `1.0` | Crest sharpness (`component.py:62,113`). |
| `caustics` | slider (0–2) | `0.6` | Caustics gain (`component.py:63,114`). |
| `wind_direction` | float deg (0–360) | `45.0` | Wind heading (`component.py:65,115`). |
| `wind_speed` | float m/s (0–60) | `8.0` | Wind speed (`component.py:66,116`). |
| `wind_turbulence` | slider (0–1) | `0.4` | Wind turbulence (`component.py:67,117`). |
| `wind_influence` | slider (0–1) | `0.0` | Wind influence on normals (`component.py:68,118`). |
| `wind_gust` | slider (0–1) | `0.4` | Wind gusts (`component.py:69,119`). |
| `mesh_resolution` | slider (2–400) | `128.0` | Tessellation (`component.py:70,120`). |
| `chaos` | slider (0–1) | `0.5` | Gerstner chaos (`component.py:71,121`). |
| `macro_wave` | slider (0–3) | `0.5` | Macro swell (`component.py:72,122`). |
| `detail_scale` | slider (0.1–4) | `1.0` | Detail noise scale (`component.py:73,123`). |
| `detail_octaves` | slider (1–12) | `6.0` | Detail octaves (`component.py:74,124`). |
| `detail_fade` | float (20–2000) | `350.0` | Detail fade distance, m (`component.py:75,125`). |
| `interaction_enabled` | bool | `True` | Object ripples (`component.py:77,126`). |
| `interaction_strength` | slider (0–8) | `1.5` | Ripple height (`component.py:78,127`). |
| `interaction_radius` | slider (0.1–4) | `1.0` | Ripple radius (`component.py:79,128`). |
| `sim_damping` | slider (0–0.3) | `0.04` | Interaction wave damping (`component.py:81,129`). |
| `sim_propagation` | slider (2–60) | `18.0` | Interaction wave speed (`component.py:82,130`). |
| `sim_saturation` | slider (0.5–10) | `4.0` | Interaction saturation (`component.py:83,131`). |
| `sim_disp_scale` | slider (0–4) | `1.0` | Interaction displacement scale (`component.py:84,132`). |
| `sim_normal_scale` | slider (0–4) | `1.0` | Interaction normal scale (`component.py:85,133`). |
| `pond_size` | float (2–4000) | `200.0` | Pond extent (`component.py:86,134`). |
## Performance notes
Cost scales with `mesh_resolution` (2–400 tessellation grid, default `128.0`) and `detail_octaves` (1–12, default `6.0`); lower both for cheap water. `detail_fade` (`20–2000`, default `350.0`) culls detail normals with distance.
## Usage in Editor
Ocean for open water (Infinite Ocean on), Pond + Pond Size for lakes. Tune Waves amplitudes by wavelength (long swells first), then Foam/Shore Fade at coasts.
## Usage in Code
```python
from core.components.rendering.environment.waterp.component import Water
w = Water()
entity.add_component(w)
w.surface_type = "Pond"
w.pond_size = 60.0
w.wind_speed = 4.0
w.foam_strength = 1.4
w.mesh_resolution = 128.0
w.detail_octaves = 6.0
```
## Related pages
- [Skybox](./skybox.md), [Procedural Sky](./procedural-sky.md), [Materials](./materials.md)
## Verified against
- `core/components/rendering/environment/waterp/component.py:14-134,136-236`
- `core/renderer/water_pass.py`
