# Вода
> Водная поверхность: `Water` (`core/components/rendering/environment/waterp/component.py:26-236`), шейдер `core/shaders/materials/Water.shader`, рисует `core/renderer/water_pass.py`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`surface_type`: `Ocean` (бесконечный, `ocean_size`) или `Pond` (`pond_size`). Восемь волн по умолчанию `waves[i] = {direction, amplitude, wavelength, speed, steepness}` (`component.py:14-23`). Блоки interaction + sim дают береговые ряби (`sim_damping / sim_propagation / sim_saturation / sim_disp_scale / sim_normal_scale`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `material_path` | путь шейдера | `core/shaders/materials/Water.shader` | Шейдер воды (`component.py:34,92`). |
| `surface_type` | enum `Ocean/Pond` | `Ocean` | Океан vs пруд (`component.py:35,93`). |
| `infinite_ocean` | bool | `True` | Плоскость за камерой (`component.py:36,94`). |
| `ocean_size` | float (10–5000) | `2000.0` | Размер океана (`component.py:37,95`). |
| `waves` | list 8×{direction, amplitude, wavelength, speed, steepness} | см. код | Поезд волн (`component.py:38-44,96`). |
| `deep_color` | color3 | `[0.02, 0.18, 0.28]` | Глубина (`component.py:45,97`). |
| `shallow_color` | color3 | `[0.10, 0.42, 0.52]` | Мель (`component.py:46,98`). |
| `foam_color` | color3 | `[0.92, 0.97, 1.0]` | Пена (`component.py:47,99`). |
| `sss_color` | color3 | `[0.0, 0.55, 0.45]` | Подповерхностное (`component.py:48,100`). |
| `horizon_color` | color3 | `[0.62, 0.78, 0.86]` | Дальний тинт (`component.py:49,101`). |
| `smoothness` | слайдер (0–1) | `0.96` | Резкость блика (`component.py:50,102`). |
| `distortion` | слайдер (0–0.3) | `0.04` | Волнение преломления (`component.py:51,103`). |
| `normal_strength` | слайдер (0–1) | `0.35` | Детали нормалей (`component.py:52,104`). |
| `wave_tiling` | слайдер (0.01–2) | `0.25` | Тайлинг волн (`component.py:53,105`). |
| `warp_amount` | слайдер (0–4) | `1.6` | Домен-варп (`component.py:54,106`). |
| `detail_speed` | слайдер (0–3) | `1.0` | Скорость деталей (`component.py:56,107`). |
| `refract_strength` | слайдер (0–1) | `0.55` | Тинт преломления (`component.py:57,108`). |
| `fresnel_power` | слайдер (0.5–8) | `4.0` | Степень Френеля (`component.py:58,109`). |
| `foam_strength` | слайдер (0–3) | `1.0` | Сила пены (`component.py:59,110`). |
| `specular` | слайдер (0–4) | `1.0` | Солнечный блик (`component.py:60,111`). |
| `shore_fade` | float (0.1–20) | `3.0` | Затухание у берега, м (`component.py:61,112`). |
| `choppiness` | слайдер (0–2) | `1.0` | Острота гребней (`component.py:62,113`). |
| `caustics` | слайдер (0–2) | `0.6` | Каустика (`component.py:63,114`). |
| `wind_direction` | float град (0–360) | `45.0` | Направление ветра (`component.py:65,115`). |
| `wind_speed` | float м/с (0–60) | `8.0` | Скорость ветра (`component.py:66,116`). |
| `wind_turbulence` | слайдер (0–1) | `0.4` | Турбулентность ветра (`component.py:67,117`). |
| `wind_influence` | слайдер (0–1) | `0.0` | Влияние ветра на нормали (`component.py:68,118`). |
| `wind_gust` | слайдер (0–1) | `0.4` | Порывы ветра (`component.py:69,119`). |
| `mesh_resolution` | слайдер (2–400) | `128.0` | Тесселяция (`component.py:70,120`). |
| `chaos` | слайдер (0–1) | `0.5` | Хаос Герстнера (`component.py:71,121`). |
| `macro_wave` | слайдер (0–3) | `0.5` | Макро-зыбь (`component.py:72,122`). |
| `detail_scale` | слайдер (0.1–4) | `1.0` | Масштаб деталей (`component.py:73,123`). |
| `detail_octaves` | слайдер (1–12) | `6.0` | Октавы деталей (`component.py:74,124`). |
| `detail_fade` | float (20–2000) | `350.0` | Дистанция затухания деталей, м (`component.py:75,125`). |
| `interaction_enabled` | bool | `True` | Ряби от объектов (`component.py:77,126`). |
| `interaction_strength` | слайдер (0–8) | `1.5` | Высота ряби (`component.py:78,127`). |
| `interaction_radius` | слайдер (0.1–4) | `1.0` | Радиус ряби (`component.py:79,128`). |
| `sim_damping` | слайдер (0–0.3) | `0.04` | Затухание волн интеракции (`component.py:81,129`). |
| `sim_propagation` | слайдер (2–60) | `18.0` | Скорость волн интеракции (`component.py:82,130`). |
| `sim_saturation` | слайдер (0.5–10) | `4.0` | Насыщение волн (`component.py:83,131`). |
| `sim_disp_scale` | слайдер (0–4) | `1.0` | Масштаб смещения ряби (`component.py:84,132`). |
| `sim_normal_scale` | слайдер (0–4) | `1.0` | Масштаб нормалей ряби (`component.py:85,133`). |
| `pond_size` | float (2–4000) | `200.0` | Размер пруда (`component.py:86,134`). |
## Performance notes
Стоимость растёт с `mesh_resolution` (сетка тесселяции 2–400, дефолт `128.0`) и `detail_octaves` (1–12, дефолт `6.0`); для дешёвой воды снижайте оба. `detail_fade` (`20–2000`, дефолт `350.0`) убирает детали нормалей с дистанцией.
## Usage in Editor
Ocean для открытой воды (Infinite Ocean вкл), Pond + Pond Size для озёр. Амплитуды волн — по длине (длинные зыби первые), затем Foam/Shore Fade у берегов.
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
