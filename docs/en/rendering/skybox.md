# Skybox
> HDRI background + IBL: `Skybox` (`core/components/rendering/environment/skybox.py:110-297`), shader `core/shaders/materials/Skybox.shader`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Equirect float texture with mipmap + repeat (`skybox.py:57-88`); LDR files normalized when `max > 1.0`. `affect_environment` builds IBL via `get_sky_ibl` (0.15 s cooldown); `show_background = False` keeps IBL only (`skybox.py:206-224`). Rendered as camera-centered cube, depth/cull off (`skybox.py:256-265`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `skybox_path` | resource path (hdr/exr) | `""` | Equirect image (`skybox.py:130,147`). |
| `show_background` | bool | `True` | Draw sky cube (`skybox.py:131,148`). |
| `affect_environment` | bool | `True` | Feed IBL (`skybox.py:132,149`). |
| `exposure` | slider (−10…10) | `0.0` | EV offset (`skybox.py:134,150`). |
| `intensity` | slider (0–8) | `1.0` | Multiplier (`skybox.py:135,151`). |
| `tint` | color3 | `[1.0, 1.0, 1.0]` | Tint (`skybox.py:136,152`). |
| `saturation` | slider (0–2) | `1.0` | Saturation (`skybox.py:137,153`). |
| `blur` | slider (0–8) | `0.0` | Background blur (`skybox.py:138,154`). |
| `flip_y` | bool | `False` | Flip V (`skybox.py:139,155`). |
| `rotation` | vec3 deg | `[0, 0, 0]` | Yaw/pitch/roll (`skybox.py:141,156`). |
## Usage in Editor
Assign HDRI/EXR, tune Exposure/Intensity, rotate with Rotation. Disable Show Background for IBL-only studio lighting.
## Usage in Code
```python
sky.skybox_path = "assets/sky/studio.hdr"
sky.exposure = -0.5
sky.rotation = Vec3(30.0, 0.0, 0.0)
```
## Related pages
- [Procedural Sky](./procedural-sky.md), [Atmosphere](./atmosphere.md), [Materials](./materials.md)
## Verified against
- `core/components/rendering/environment/skybox.py:110-162,206-297`
- `core/shaders/materials/Skybox.shader`
