# Atmosphere

Physical atmosphere scattering layered around the sun and horizon — blue noon, orange dusk, hazy distance.

| Inspector group / field | Meaning |
|---|---|
| Atmosphere: Enabled / Intensity | Master switch and strength |
| Sun / Sun Intensity | Coupled sun source reference |
| LUT Resolution | Scattering lookup density (quality vs cost) |
| Ozone Factor / Aerosol Scale | Air composition tint (blue vs smog) |
| Rayleigh Scale / Mie Anisotropy / Mie Albedo | Sky-blue vs haze-forward balance |
| Ground Albedo / Multiscatter | Bounce-light approximation from below |
| Horizon Haze | Low-angle whitening amount |

Works on top of Procedural Sky; Skybox HDRI scenes usually leave it off to avoid double tinting (the photo already contains air).

Workflows: alpine noon — high Rayleigh, low aerosol, crisp blue; desert dusk — high Mie anisotropy forward lobe, warm ground albedo; smog city — aerosol up, ozone shifted, horizon haze strong.

LUT resolution is the cost knob: raise until banding leaves, then stop — beyond that only the profiler notices.

Troubleshooting: purple sky → ozone/aerosol pushed past plausible, reset toward defaults; no visible effect → intensity zero or HDRI skybox dominating; white horizon wall → haze maxed, lower gradually.

Related: Procedural Sky, Directional Light, Clouds, Fog.
