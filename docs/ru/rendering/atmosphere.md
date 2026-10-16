# Атмосфера
> Физичный LUT неба: `Atmosphere` (`core/components/rendering/environment/atmosp/component.py:11-163`), одна на сцену через `Atmosphere.for_scene(scene)`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Считает LUT пропускания/мультирассеяния (`atmosp/compute.py`, `AtmosphereLutMixin`); `_resolution_scale` масштабирует LUT. Используется первый включённый инстанс сцены (`for_scene`). Все поля с подчёркиванием и так сериализуются.
## Properties
| name | type | default | description |
|---|---|---|---|
| `_intensity` | слайдер (1–60) | `20.0` | Усиление неба (`component.py:20,42`). |
| `_sun_intensity` | слайдер (0–10) | `1.0` | Диск солнца (`component.py:22,43`). |
| `_resolution_scale` | слайдер (0.25–1) | `1.0` | Разрешение LUT (`component.py:23,44`). |
| `_ozone_factor` | слайдер (0–3) | `1.0` | Поглощение озоном (`component.py:24,45`). |
| `_aerosol_scale` | слайдер (0–5) | `1.0` | Аэрозоли/Mie (`component.py:25,46`). |
| `_rayleigh_scale` | слайдер (0–3) | `1.0` | Рэлей (`component.py:26,47`). |
| `_mie_anisotropy` | слайдер (0–0.95) | `0.76` | Параметр g (`component.py:27,48`). |
| `_mie_albedo` | слайдер (0–1) | `0.9` | Альбедо Mie (`component.py:28,49`). |
| `_ground_albedo` | слайдер (0–1) | `0.3` | Отскок от земли (`component.py:29,50`). |
| `_multiscatter_strength` | слайдер (0–2) | `1.0` | Мультирассеяние (`component.py:30,51`). |
| `_horizon_haze` | слайдер (0–2) | `0.4` | Дымка горизонта (`component.py:31,52`). |
| `_planet_radius` | слайдер км (6000–6500) | `6360.0` | Радиус планеты (`component.py:32,53`). |
| `_atmosphere_height` | слайдер км (20–120) | `60.0` | Высота верха (`component.py:33,54`). |
| `_camera_height_m` | слайдер м (0–8000) | `20.0` | Высота глаз (`component.py:34,55`). |
| `_sun_angular_radius` | слайдер град (0.05–1) | `0.27` | Размер солнца (`component.py:35,56`). |
| `_sun_limb_darkening` | слайдер (0–1) | `0.7` | Потемнение лимба (`component.py:36,57`). |
| `_sun_convergence` | слайдер (0–1) | `0.5` | Мягкость края (`component.py:37,58`). |
| `_color_temperature` | слайдер K (2000–20000) | `5778.0` | Чернотелый спектр (`component.py:38,59`). |
## Usage in Editor
Одна Atmosphere на сцену. `_rayleigh_scale` ниже — чужое небо, `_aerosol_scale`/`_horizon_haze` выше — пыльный закат.
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
