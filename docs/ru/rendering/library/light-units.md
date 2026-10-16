# Физические единицы света

> Физические единицы по типам света — люксы для направленного, люмены для point/spot, ниты для area — таблицы-референсы из кода, legacy-множители и гайд по настройке.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Базовый класс `Light` (`core/components/lighting/lightp/light.py:25-90`) хранит `color`, `intensity` (0–200000) и `cast_shadows`. Единицы зависят от конкретного типа: `DirectionalLight` — люксы, `PointLight`/`SpotLight` — люмены, `AreaLight` — ниты. Внутренние коэффициенты перевода: `LUX_TO_RADIANCE = 1e-5`, `CANDELA_TO_RADIANCE = 1e-2`, `AREA_TO_RADIANCE = 1e-2` (`light.py:33-35`).

## Properties

| name | type | default | description |
|---|---|---|---|
| `DirectionalLight.intensity` | float, люксы | `100000.0` | Освещённость в люксах (`light.py:198,205`). |
| `PointLight.intensity` | float, люмены | `1000.0` | Полный световой поток в люменах (`light.py:241,248`). |
| `SpotLight.intensity` | float, люмены в конусе | `1000.0` | Те же люмены, сконцентрированные углом конуса (`light.py:317,327`). |
| `AreaLight.intensity` | float, ниты | `100.0` | Яркость поверхности в кд/м² (`shadows.py:92,106`). |
| `Light.color` | color3 | `[1,1,1]` | Цвет света (`light.py:72,78`). |
| `Light.cast_shadows` | bool | `True` | Вклад в карты теней (`light.py:74,80`). |

## Таблицы-референсы (ключи verbatim, `light.py:40-64`)

`LUX_REFERENCE`: `direct_sun 100000.0`, `full_daylight 15000.0`, `overcast_day 5000.0`, `sunrise_sunset 400.0`, `office 500.0`, `twilight 10.0`, `full_moon 0.2`.
`LUMEN_REFERENCE`: `candle 12.57`, `decorative_led 100.0`, `desk_lamp 300.0`, `room_ceiling 800.0`, `bulb_100w 1600.0`, `street_light 15000.0`, `stadium_floodlight 100000.0`.
`NIT_REFERENCE`: `monitor_sdr 200.0`, `office_panel 500.0`, `overcast_sky 2000.0`, `clear_sky 5000.0`, `led_softbox 10000.0`.

## Гайд по настройке

- **Солнце**: `DirectionalLight`, `intensity = 100000.0` (прямое солнце). Пасмурный день — `5000`, закат — `400`.
- **Лампочка**: `PointLight`, `intensity = 1000.0` (≈100 Вт — 1600 лм; настольная лампа — 300 лм). `range` — по размеру комнаты.
- **Фонарик/спот**: `SpotLight`, `intensity = 1000.0`; узкие конусы выглядят ярче, т.к. те же люмены сконцентрированы.
- **Софтбокс/панель**: `AreaLight`, `intensity = 100.0` по умолчанию; офисная панель ≈ 500 нит, LED-софтбокс ≈ 10000 нит. Панель большей площади при тех же нитах даёт больше света сцене.
- **Заполняющий свет**: выключайте `cast_shadows`.

## Миграция legacy

Сцены с `light_version < 2` автоматически рескейлятся (`light.py:36-38,166-167`): `LEGACY_DIRECTIONAL_MULT = 100000.0`, `LEGACY_POINT_MULT = 2000.0` (применяется и к спотам), `LEGACY_AREA_MULT = 100.0`. Новые сцены сериализуют `light_version: 2` (`light.py:134`).

## Usage in Code

```python
from core.components.lighting.lightp.light import DirectionalLight
sun = entity.get_component(DirectionalLight)
sun.color = [1.0, 0.95, 0.9]
sun.intensity = 100000.0
sun.cast_shadows = True
```

## Related pages

- [Свет](../light.md), [Направленный свет](../directional-light.md), [Точечный свет](../point-light.md), [Спот](../spot-light.md), [Area-свет](../area-light.md), [Тени](../shadows.md)

## Verified against

- `core/components/lighting/lightp/light.py:33-90` (коэффициенты, legacy-множители, `LUX/LUMEN/NIT_REFERENCE`, базовые дефолты)
- `core/components/lighting/lightp/light.py:198-205,241-248,317-327` (единицы и дефолты по типам в инспекторе)
- `core/components/lighting/lightp/shadows.py:92-113` (инспектор `AreaLight` в нитах, дефолт `intensity = 100.0`)
