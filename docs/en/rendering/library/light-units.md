# Light Units

> Physical units per light type — lux for directional, lumens for point/spot, nits for area — the in-code reference tables, legacy multipliers, and a setup guide.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Base class `Light` (`core/components/lighting/lightp/light.py:25-90`) stores `color`, `intensity` (0–200000), and `cast_shadows`. Units depend on the concrete type: `DirectionalLight` reads lux, `PointLight`/`SpotLight` read lumens, `AreaLight` reads nits. Internal conversion factors are `LUX_TO_RADIANCE = 1e-5`, `CANDELA_TO_RADIANCE = 1e-2`, `AREA_TO_RADIANCE = 1e-2` (`light.py:33-35`).

## Properties

| name | type | default | description |
|---|---|---|---|
| `DirectionalLight.intensity` | float, lux | `100000.0` | Illuminance in lux (`light.py:198,205`). |
| `PointLight.intensity` | float, lumens | `1000.0` | Total flux in lumens (`light.py:241,248`). |
| `SpotLight.intensity` | float, lumens in cone | `1000.0` | Same lumens concentrated by the cone angle (`light.py:317,327`). |
| `AreaLight.intensity` | float, nits | `100.0` | Surface brightness in cd/m² (`shadows.py:92,106`). |
| `Light.color` | color3 | `[1,1,1]` | Light color (`light.py:72,78`). |
| `Light.cast_shadows` | bool | `True` | Shadow-map contribution (`light.py:74,80`). |

## Reference tables (verbatim keys, `light.py:40-64`)

`LUX_REFERENCE`: `direct_sun 100000.0`, `full_daylight 15000.0`, `overcast_day 5000.0`, `sunrise_sunset 400.0`, `office 500.0`, `twilight 10.0`, `full_moon 0.2`.
`LUMEN_REFERENCE`: `candle 12.57`, `decorative_led 100.0`, `desk_lamp 300.0`, `room_ceiling 800.0`, `bulb_100w 1600.0`, `street_light 15000.0`, `stadium_floodlight 100000.0`.
`NIT_REFERENCE`: `monitor_sdr 200.0`, `office_panel 500.0`, `overcast_sky 2000.0`, `clear_sky 5000.0`, `led_softbox 10000.0`.

## Setup guide

- **Sun**: `DirectionalLight`, `intensity = 100000.0` (direct sun). Overcast day `5000`, sunrise `400`.
- **Bulb/lamp**: `PointLight`, `intensity = 1000.0` (≈100 W bulb is 1600 lm; desk lamp 300 lm). Set `range` to the room size.
- **Flashlight/spot**: `SpotLight`, `intensity = 1000.0`; narrow cones look brighter because the same lumens are concentrated.
- **Softbox/panel**: `AreaLight`, `intensity = 100.0` default; office panel ≈ 500 nits, LED softbox ≈ 10000 nits. Bigger panels emit more total light at equal nits.
- **Fill lights**: switch `cast_shadows` off.

## Legacy migration

Scenes with `light_version < 2` are auto-rescaled (`light.py:36-38,166-167`): `LEGACY_DIRECTIONAL_MULT = 100000.0`, `LEGACY_POINT_MULT = 2000.0` (also applied to spot), `LEGACY_AREA_MULT = 100.0`. New scenes serialize `light_version: 2` (`light.py:134`).

## Usage in Code

```python
from core.components.lighting.lightp.light import DirectionalLight
sun = entity.get_component(DirectionalLight)
sun.color = [1.0, 0.95, 0.9]
sun.intensity = 100000.0
sun.cast_shadows = True
```

## Related pages

- [Light](../light.md), [Directional Light](../directional-light.md), [Point Light](../point-light.md), [Spot Light](../spot-light.md), [Area Light](../area-light.md), [Shadows](../shadows.md)

## Verified against

- `core/components/lighting/lightp/light.py:33-90` (conversion factors, legacy mults, `LUX/LUMEN/NIT_REFERENCE`, base defaults)
- `core/components/lighting/lightp/light.py:198-205,241-248,317-327` (per-type inspector units and defaults)
- `core/components/lighting/lightp/shadows.py:92-113` (`AreaLight` nits inspector, `intensity = 100.0` default)
