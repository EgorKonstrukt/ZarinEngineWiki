# Процедурное небо
> Аналитическое небо день/ночь: `ProceduralSky` (`core/components/rendering/environment/skyp/component.py:18-303`), шейдер `core/shaders/materials/Sky.shader`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Миксины: часы astro (`skyp/astro.py`), градация атмосферы (`skyp/atmosphere.py`), солнце (`skyp/sun.py`), облака (`skyp/clouds.py`). `on_update` крутит `time_speed` игровых-сек/сек и синкает связанный свет солнца (`component.py:164-228`). Дефолты луны: текстура `core/textures/moon.tga`, радиус `0.27` град.
## Properties
| name | type | default | description |
|---|---|---|---|
| `material_path` | путь шейдера | `core/shaders/materials/Sky.shader` | Шейдер неба (`component.py:114`). |
| `night_sky_enabled` | bool | `True` | Ночной слой (`component.py:43,116`). |
| `night_exposure` | слайдер (0–3) | `1.0` | Усиление ночи (`component.py:44,117`). |
| `star_enabled` | bool | `True` | Звёзды (`component.py:46,118`). |
| `star_density` | слайдер (0–1) | `0.45` | Покрытие (`component.py:47,119`). |
| `star_intensity` | слайдер (0–5) | `1.0` | Яркость (`component.py:48,120`). |
| `star_scale` | слайдер (20–200) | `80.0` | Размер (`component.py:49,121`). |
| `star_twinkle` | слайдер (0–1) | `0.5` | Мерцание (`component.py:50,122`). |
| `star_twinkle_speed` | слайдер (0–10) | `1.0` | Темп мерцания (`component.py:51,123`). |
| `star_seed` | слайдер (0–100) | `1.0` | Сид узора (`component.py:52,124`). |
| `star_color` | color3 | `[0.9, 0.93, 1.0]` | Тинт (`component.py:53,125`). |
| `milky_way_enabled` | bool | `True` | Млечный путь (`component.py:55,126`). |
| `milky_way_intensity` | слайдер (0–3) | `0.6` | Усиление рукава (`component.py:56,127`). |
| `milky_way_pole` | vec3 | `[0.4, 0.3, 0.85]` | Ориентация рукава (`component.py:57,128`). |
| `year` | int (1–9999) | `2024` | Год календаря (`component.py:59,129`, `astro.py:11`). |
| `month` | int (1–12) | `6` | Месяц (`component.py:60,130`, `astro.py:12`). |
| `day` | int (1–31) | `21` | День (`component.py:61,131`, `astro.py:13`). |
| `hour` | int (0–23) | `12` | Час (`component.py:62,132`, `astro.py:14`). |
| `minute` | int (0–59) | `0` | Минута (`component.py:63,133`, `astro.py:15`). |
| `second` | int (0–59) | `0` | Секунда (`component.py:64,134`, `astro.py:16`). |
| `time_speed` | float | `0.0` | Игровых-сек на реал. сек (`component.py:65,138`). |
| `latitude` | слайдер град (−90…90) | `55.75` | Широта наблюдателя (`component.py:67`, `astro.py:17`). |
| `longitude` | слайдер град (−180…180) | `37.62` | Долгота наблюдателя (`component.py:68`, `astro.py:18`). |
| `utc_offset` | слайдер ч (−12…14) | `3.0` | Смещение UTC (`component.py:69`, `astro.py:19`). |
| `sun_light_entity_id` | gameobject | `""` | Связанный [Directional Light](./directional-light.md) (`component.py:71,141`). |
| `moon_enabled` | bool | `True` | Луна (`component.py:73,142`). |
| `moon_size` | слайдер град (0.05–1.5) | `0.27` | Угловой радиус (`component.py:74,143`). |
| `moon_intensity` | слайдер (0–5) | `1.0` | Яркость (`component.py:75,144`). |
| `moon_texture_path` | путь картинки | `core/textures/moon.tga` | Альбедо луны (`component.py:76,145`). |
| `environment_path` | путь | `""` | Доп. слот окружения (`component.py:115`). |
| `_astro_obliquity_deg` | слайдер град (0–90) | `23.439` | Наклон оси (`component.py:78`, `astro.py:32`). |
| `_astro_obliquity_drift` | слайдер (0–0.001) | `0.0000004` | Дрейф наклона град/сут (`component.py:79`, `astro.py:33`). |
| `_astro_sun_ml_epoch` | слайдер град (0–360) | `280.459` | Средняя долгота Солнца (`component.py:81`, `astro.py:34`). |
| `_astro_sun_ml_rate` | слайдер (0–2) | `0.98564736` | Темп долготы Солнца град/сут (`component.py:82`, `astro.py:35`). |
| `_astro_sun_ma_epoch` | слайдер град (0–360) | `357.5291` | Средняя аномалия Солнца (`component.py:83`, `astro.py:36`). |
| `_astro_sun_ma_rate` | слайдер (0–2) | `0.98560028` | Темп аномалии Солнца (`component.py:84`, `astro.py:37`). |
| `_astro_sun_ecc` | слайдер (0–3) | `1.915` | Уравнение центра Солнца (`component.py:85`, `astro.py:38`). |
| `_astro_sun_ecc2` | слайдер (0–1) | `0.020` | Гармоника эксцентриситета (`component.py:86`, `astro.py:39`). |
| `_astro_moon_ml_epoch` | слайдер град (0–360) | `218.316` | Средняя долгота Луны (`component.py:88`, `astro.py:40`). |
| `_astro_moon_ml_rate` | слайдер (0–15) | `13.176396` | Темп долготы Луны (`component.py:89`, `astro.py:41`). |
| `_astro_moon_ma_epoch` | слайдер град (0–360) | `134.963` | Средняя аномалия Луны (`component.py:90`, `astro.py:42`). |
| `_astro_moon_ma_rate` | слайдер (0–15) | `13.064993` | Темп аномалии Луны (`component.py:91`, `astro.py:43`). |
| `_astro_moon_el_epoch` | слайдер град (0–360) | `297.850` | Элонгация Луны (`component.py:92`, `astro.py:44`). |
| `_astro_moon_el_rate` | слайдер (0–15) | `12.190749` | Темп элонгации (`component.py:93`, `astro.py:45`). |
| `_astro_moon_af_epoch` | слайдер град (0–360) | `93.272` | Аргумент широты Луны (`component.py:94`, `astro.py:46`). |
| `_astro_moon_af_rate` | слайдер (0–15) | `13.229350` | Темп аргумента (`component.py:95`, `astro.py:47`). |
| `_astro_moon_lat1` | слайдер (0–15) | `5.128` | Наклонение орбиты Луны (`component.py:96`, `astro.py:53`). |
| `_astro_moon_lon1` | слайдер (−10…10) | `6.289` | Ампл. долготы sin(M) (`component.py:97`, `astro.py:48`). |
| `_astro_moon_lon2` | слайдер (−5…5) | `-1.274` | Ампл. sin(2D−M) (`component.py:98`, `astro.py:49`). |
| `_astro_moon_lon3` | слайдер (−5…5) | `0.658` | Ампл. sin(2D) (`component.py:99`, `astro.py:50`). |
| `_astro_moon_lon4` | слайдер (−5…5) | `-0.186` | Ампл. sin(Ms) (`component.py:100`, `astro.py:51`). |
| `_astro_moon_lon5` | слайдер (−5…5) | `-0.060` | Ампл. sin(2M−2D) (`component.py:101`, `astro.py:52`). |
| `_astro_moon_lat2` | слайдер (−5…5) | `0.280` | Ампл. широты sin(M+F) (`component.py:102`, `astro.py:54`). |
| `_astro_moon_lat3` | слайдер (−5…5) | `0.277` | Ампл. sin(M−F) (`component.py:103`, `astro.py:55`). |
| `_astro_moon_lat4` | слайдер (−5…5) | `-0.017` | Ампл. sin(2D−F) (`component.py:104`, `astro.py:56`). |
| `_astro_synodic_month` | слайдер сут (20–40) | `29.530588853` | Синодический месяц (`component.py:106`, `astro.py:57`). |
| `_astro_new_moon_jd` | float (0–3000000) | `2451550.258` | Эпоха новолуния JD (`component.py:107`, `astro.py:58`). |
| `_astro_gmst_epoch` | слайдер ч (0–24) | `18.697374558` | Эпоха GMST (`component.py:108`, `astro.py:59`). |
| `_astro_gmst_rate` | слайдер ч/сут (23–25) | `24.06570982441908` | Темп GMST (`component.py:109`, `astro.py:60`). |
## Usage in Editor
Дата/время + точка, связь Sun Light, Time Speed `60` для суточного пролёта. Stars/Milky Way — для ночных кадров.
## Usage in Code
```python
from core.components.rendering.environment.skyp.component import ProceduralSky
psky = ProceduralSky()
entity.add_component(psky)
psky.hour = 18
psky.time_speed = 60.0
psky.sun_light_entity_id = sun_entity.id
```
## Related pages
- [Skybox](./skybox.md), [Directional Light](./directional-light.md), [Atmosphere](./atmosphere.md), [Clouds](./clouds.md)
## Verified against
- `core/components/rendering/environment/skyp/component.py:40-160,164-228`
- `core/components/rendering/environment/skyp/astro.py:11-19` (дефолты времени/точки), `:31-61` (`_ASTRO_DEFAULTS`)
