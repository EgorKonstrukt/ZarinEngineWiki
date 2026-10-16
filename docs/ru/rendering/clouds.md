# Облака
> Объёмный слой облаков: `Cloud` (`core/components/rendering/environment/clouds.py:17-273`), шейдер `core/shaders/materials/Clouds.shader` (+ плоский `CloudLayer.shader`).
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Полноэкранный реймарш (`render_clouds`) использует `u_time`, `u_cam_pos`, `u_inv_view_proj`, `_Coverage…_Tint` и солнце/цвет из [Directional Light](./directional-light.md) (`clouds.py:75-150`). Плоский фолбэк (`render_cloud_layer`) пропускается при `height < 8.0` или `opacity <= 0.001`, рисует плоскость `1400` м на `height + thickness*0.5` за камерой (`clouds.py:152-217`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `material_path` | путь шейдера | `core/shaders/materials/Clouds.shader` | Шейдер облаков (`clouds.py:24,52`). |
| `coverage` | слайдер (0–1) | `0.78` | Покрытие (`clouds.py:25,53`). |
| `density` | слайдер (0–2) | `1.12` | Оптическая плотность (`clouds.py:26,54`). |
| `speed` | float (0–5) | `0.38` | Скорость ветра (`clouds.py:27,55`). |
| `wind_direction` | float град (0–360) | `18.0` | Направление ветра (`clouds.py:28,56`). |
| `height` | float (0–500) | `82.0` | База слоя, м (`clouds.py:29,57`). |
| `thickness` | float (0–200) | `42.0` | Толщина, м (`clouds.py:30,58`). |
| `scale` | float (0.001–1) | `0.014` | Масштаб шума (`clouds.py:31,59`). |
| `opacity` | слайдер (0–1) | `0.68` | Альфа (`clouds.py:32,60`). |
| `shadow_strength` | слайдер (0–1) | `0.52` | Самозатенение (`clouds.py:33,61`). |
| `fog_density` | слайдер (0–1) | `0.0` | Туман внутри (`clouds.py:34,62`). |
| `height_falloff` | float (0–4) | `0.025` | Вертикальное затухание (`clouds.py:35,63`). |
| `scattering` | слайдер (0–2) | `1.05` | Прямое рассеяние (`clouds.py:36,64`). |
| `absorption` | слайдер (0–4) | `0.92` | Поглощение Бера (`clouds.py:37,65`). |
| `softness` | слайдер (0.02–0.8) | `0.28` | Мягкость краёв (`clouds.py:38,66`). |
| `detail_strength` | слайдер (0–1) | `0.92` | Эррозия деталей (`clouds.py:39,67`). |
| `steps` | int слайдер (4–32) | `16` | Шаги марша (`clouds.py:40,68`). |
| `evolution` | слайдер (0–2) | `1.1` | Морфинг во времени (`clouds.py:41,69`). |
| `contrast` | слайдер (0.2–2.5) | `1.7` | Контраст плотности (`clouds.py:42,70`). |
| `silver_lining` | слайдер (0–2) | `1.35` | Серебристая кайма (`clouds.py:43,71`). |
| `seed` | float (0–10000) | `11.3` | Сид шума (`clouds.py:44,72`). |
| `tint` | color3 | `[0.86, 0.92, 1.0]` | Тинт (`clouds.py:45,73`). |
## Usage in Editor
Coverage/Density выше — пасмурно, Steps — качество (цена), Silver Lining — контровый край. Height держать ≥ 8 для плоского фолбэка.
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
