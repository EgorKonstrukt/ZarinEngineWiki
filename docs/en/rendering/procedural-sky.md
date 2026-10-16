# Procedural Sky
> Analytic day/night sky: `ProceduralSky` (`core/components/rendering/environment/skyp/component.py:18-303`), shader `core/shaders/materials/Sky.shader`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Mixins: astro clock (`skyp/astro.py`), atmosphere grading (`skyp/atmosphere.py`), sun (`skyp/sun.py`), clouds (`skyp/clouds.py`). `on_update` advances `time_speed` game-sec/sec and syncs the linked sun light (`component.py:164-228`). Moon defaults: texture `core/textures/moon.tga`, angular radius `0.27` deg.
## Properties
| name | type | default | description |
|---|---|---|---|
| `material_path` | shader path | `core/shaders/materials/Sky.shader` | Sky shader (`component.py:114`). |
| `night_sky_enabled` | bool | `True` | Night layer (`component.py:43,116`). |
| `night_exposure` | slider (0–3) | `1.0` | Night gain (`component.py:44,117`). |
| `star_enabled` | bool | `True` | Stars (`component.py:46,118`). |
| `star_density` | slider (0–1) | `0.45` | Star coverage (`component.py:47,119`). |
| `star_intensity` | slider (0–5) | `1.0` | Brightness (`component.py:48,120`). |
| `star_scale` | slider (20–200) | `80.0` | Star size (`component.py:49,121`). |
| `star_twinkle` | slider (0–1) | `0.5` | Twinkle amount (`component.py:50,122`). |
| `star_twinkle_speed` | slider (0–10) | `1.0` | Twinkle rate (`component.py:51,123`). |
| `star_seed` | slider (0–100) | `1.0` | Pattern seed (`component.py:52,124`). |
| `star_color` | color3 | `[0.9, 0.93, 1.0]` | Tint (`component.py:53,125`). |
| `milky_way_enabled` | bool | `True` | Milky way (`component.py:55,126`). |
| `milky_way_intensity` | slider (0–3) | `0.6` | Band gain (`component.py:56,127`). |
| `milky_way_pole` | vec3 | `[0.4, 0.3, 0.85]` | Band orientation (`component.py:57,128`). |
| `year` | int (1–9999) | `2024` | Calendar year (`component.py:59,129`, `astro.py:11`). |
| `month` | int (1–12) | `6` | Calendar month (`component.py:60,130`, `astro.py:12`). |
| `day` | int (1–31) | `21` | Calendar day (`component.py:61,131`, `astro.py:13`). |
| `hour` | int (0–23) | `12` | Hour (`component.py:62,132`, `astro.py:14`). |
| `minute` | int (0–59) | `0` | Minute (`component.py:63,133`, `astro.py:15`). |
| `second` | int (0–59) | `0` | Second (`component.py:64,134`, `astro.py:16`). |
| `time_speed` | float | `0.0` | Game-sec per real sec (`component.py:65,138`). |
| `latitude` | slider deg (−90…90) | `55.75` | Observer latitude (`component.py:67`, `astro.py:17`). |
| `longitude` | slider deg (−180…180) | `37.62` | Observer longitude (`component.py:68`, `astro.py:18`). |
| `utc_offset` | slider h (−12…14) | `3.0` | UTC offset (`component.py:69`, `astro.py:19`). |
| `sun_light_entity_id` | gameobject | `""` | Linked [Directional Light](./directional-light.md) (`component.py:71,141`). |
| `moon_enabled` | bool | `True` | Moon (`component.py:73,142`). |
| `moon_size` | slider deg (0.05–1.5) | `0.27` | Angular radius (`component.py:74,143`). |
| `moon_intensity` | slider (0–5) | `1.0` | Brightness (`component.py:75,144`). |
| `moon_texture_path` | image path | `core/textures/moon.tga` | Moon albedo (`component.py:76,145`). |
| `environment_path` | string path | `""` | Extra environment slot (`component.py:115`). |
| `_astro_obliquity_deg` | slider deg (0–90) | `23.439` | Axial tilt (`component.py:78`, `astro.py:32`). |
| `_astro_obliquity_drift` | slider (0–0.001) | `0.0000004` | Tilt drift deg/day (`component.py:79`, `astro.py:33`). |
| `_astro_sun_ml_epoch` | slider deg (0–360) | `280.459` | Sun mean longitude (`component.py:81`, `astro.py:34`). |
| `_astro_sun_ml_rate` | slider (0–2) | `0.98564736` | Sun longitude rate deg/day (`component.py:82`, `astro.py:35`). |
| `_astro_sun_ma_epoch` | slider deg (0–360) | `357.5291` | Sun mean anomaly (`component.py:83`, `astro.py:36`). |
| `_astro_sun_ma_rate` | slider (0–2) | `0.98560028` | Sun anomaly rate deg/day (`component.py:84`, `astro.py:37`). |
| `_astro_sun_ecc` | slider (0–3) | `1.915` | Sun equation of center (`component.py:85`, `astro.py:38`). |
| `_astro_sun_ecc2` | slider (0–1) | `0.020` | Sun eccentricity harmonic (`component.py:86`, `astro.py:39`). |
| `_astro_moon_ml_epoch` | slider deg (0–360) | `218.316` | Moon mean longitude (`component.py:88`, `astro.py:40`). |
| `_astro_moon_ml_rate` | slider (0–15) | `13.176396` | Moon longitude rate (`component.py:89`, `astro.py:41`). |
| `_astro_moon_ma_epoch` | slider deg (0–360) | `134.963` | Moon mean anomaly (`component.py:90`, `astro.py:42`). |
| `_astro_moon_ma_rate` | slider (0–15) | `13.064993` | Moon anomaly rate (`component.py:91`, `astro.py:43`). |
| `_astro_moon_el_epoch` | slider deg (0–360) | `297.850` | Moon elongation (`component.py:92`, `astro.py:44`). |
| `_astro_moon_el_rate` | slider (0–15) | `12.190749` | Moon elongation rate (`component.py:93`, `astro.py:45`). |
| `_astro_moon_af_epoch` | slider deg (0–360) | `93.272` | Moon arg of latitude (`component.py:94`, `astro.py:46`). |
| `_astro_moon_af_rate` | slider (0–15) | `13.229350` | Moon arg rate (`component.py:95`, `astro.py:47`). |
| `_astro_moon_lat1` | slider (0–15) | `5.128` | Moon orbital inclination (`component.py:96`, `astro.py:53`). |
| `_astro_moon_lon1` | slider (−10…10) | `6.289` | Moon lon amp sin(M) (`component.py:97`, `astro.py:48`). |
| `_astro_moon_lon2` | slider (−5…5) | `-1.274` | Moon lon amp sin(2D-M) (`component.py:98`, `astro.py:49`). |
| `_astro_moon_lon3` | slider (−5…5) | `0.658` | Moon lon amp sin(2D) (`component.py:99`, `astro.py:50`). |
| `_astro_moon_lon4` | slider (−5…5) | `-0.186` | Moon lon amp sin(Ms) (`component.py:100`, `astro.py:51`). |
| `_astro_moon_lon5` | slider (−5…5) | `-0.060` | Moon lon amp sin(2M-2D) (`component.py:101`, `astro.py:52`). |
| `_astro_moon_lat2` | slider (−5…5) | `0.280` | Moon lat amp sin(M+F) (`component.py:102`, `astro.py:54`). |
| `_astro_moon_lat3` | slider (−5…5) | `0.277` | Moon lat amp sin(M-F) (`component.py:103`, `astro.py:55`). |
| `_astro_moon_lat4` | slider (−5…5) | `-0.017` | Moon lat amp sin(2D-F) (`component.py:104`, `astro.py:56`). |
| `_astro_synodic_month` | slider days (20–40) | `29.530588853` | Synodic month (`component.py:106`, `astro.py:57`). |
| `_astro_new_moon_jd` | float (0–3000000) | `2451550.258` | New moon epoch JD (`component.py:107`, `astro.py:58`). |
| `_astro_gmst_epoch` | slider h (0–24) | `18.697374558` | GMST epoch (`component.py:108`, `astro.py:59`). |
| `_astro_gmst_rate` | slider h/day (23–25) | `24.06570982441908` | GMST rate (`component.py:109`, `astro.py:60`). |
## Usage in Editor
Set date/time + site, link Sun Light, set Time Speed `60` for a day sweep. Tune Stars/Milky Way for night shots.
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
- `core/components/rendering/environment/skyp/astro.py:11-19` (time/site defaults), `:31-61` (`_ASTRO_DEFAULTS`)
