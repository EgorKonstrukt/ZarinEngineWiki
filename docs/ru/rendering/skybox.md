# Скайбокс
> HDRI-фон + IBL: `Skybox` (`core/components/rendering/environment/skybox.py:110-297`), шейдер `core/shaders/materials/Skybox.shader`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Equirect float-текстура с мипмапами + repeat (`skybox.py:57-88`); LDR нормализуется при `max > 1.0`. `affect_environment` строит IBL через `get_sky_ibl` (кулдаун 0.15 с); `show_background = False` оставляет только IBL (`skybox.py:206-224`). Рисуется кубом вокруг камеры, depth/cull выкл (`skybox.py:256-265`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `skybox_path` | путь (hdr/exr) | `""` | Equirect-картинка (`skybox.py:130,147`). |
| `show_background` | bool | `True` | Рисовать куб неба (`skybox.py:131,148`). |
| `affect_environment` | bool | `True` | Питать IBL (`skybox.py:132,149`). |
| `exposure` | слайдер (−10…10) | `0.0` | Сдвиг EV (`skybox.py:134,150`). |
| `intensity` | слайдер (0–8) | `1.0` | Множитель (`skybox.py:135,151`). |
| `tint` | color3 | `[1.0, 1.0, 1.0]` | Тинт (`skybox.py:136,152`). |
| `saturation` | слайдер (0–2) | `1.0` | Насыщенность (`skybox.py:137,153`). |
| `blur` | слайдер (0–8) | `0.0` | Блюр фона (`skybox.py:138,154`). |
| `flip_y` | bool | `False` | Отразить V (`skybox.py:139,155`). |
| `rotation` | vec3 град | `[0, 0, 0]` | Yaw/pitch/roll (`skybox.py:141,156`). |
## Usage in Editor
Назначить HDRI/EXR, подкрутить Exposure/Intensity, повернуть Rotation. Show Background выкл — студийное IBL-освещение без фона.
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
