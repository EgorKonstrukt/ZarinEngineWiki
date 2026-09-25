# Atmosphere

Physical atmosphere scattering around the sun and horizon.

| Inspector group / field | Meaning |
|---|---|
| Atmosphere: Enabled / Intensity | Master switch and strength |
| Sun / Sun Intensity | Coupled sun source |
| LUT Resolution | Scattering lookup density |
| Ozone Factor / Aerosol Scale | Air composition tint |
| Rayleigh Scale / Mie Anisotropy / Mie Albedo | Sky blue vs haze balance |
| Ground Albedo / Multiscatter | Bounce light approximation |
| Horizon Haze | Low-angle whitening |

Works on top of Procedural Sky; Skybox HDRI scenes usually leave it off to avoid double tinting.

Related: Procedural Sky, Directional Light, Clouds.
