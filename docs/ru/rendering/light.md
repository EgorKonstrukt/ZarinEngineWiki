# Свет
> Базовый компонент `Light` (`core/components/lighting/lightp/light.py`): цвет, физическая интенсивность и тумблер теней; конкретные типы задают единицы и доп. поля.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`LightType`: `directional / point / spot / area`; `LightAreaType`: `rect / disk` (`light.py:13-22`). Базовый `Light` скрыт из редактора (`_editor_hidden = True`); добавлять надо конкретный тип. `Light.deserialize` распределяет по `light_type` в `DirectionalLight / PointLight / SpotLight / AreaLight` (`light.py:147-161`). Единицы: directional — люксы, point/spot — люмены, area — ниты. Старые сцены (`light_version < 2`) пересчитываются множителями `LEGACY_DIRECTIONAL_MULT = 100000.0`, `LEGACY_POINT_MULT = 2000.0`, `LEGACY_AREA_MULT = 100.0` (`light.py:36-38`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `color` | color3 | `[1.0, 1.0, 1.0]` | Цвет света (`light.py:72,78`). |
| `intensity` | float (0–200000) | `100000.0` | Дефолт базы; типы переопределяют единицы (`light.py:73,79`). |
| `cast_shadows` | bool | `True` | Вклад в карты теней (`light.py:74,80`). |
## Usage in Editor
Добавлять Directional/Point/Spot/Area Light (не базовый Light). Цвет белый + пресеты из lux/lumen/nit таблиц в коде. `cast_shadows` выключать у заливок.
## Usage in Code
```python
from core.components.lighting.lightp.light import DirectionalLight
sun = entity.get_component(DirectionalLight)
sun.color = [1.0, 0.95, 0.9]
sun.intensity = 100000.0
sun.cast_shadows = True
```
## Related pages
- [Directional Light](./directional-light.md), [Point Light](./point-light.md), [Spot Light](./spot-light.md), [Area Light](./area-light.md), [Shadows](./shadows.md), [Projector](./projector.md)
## Verified against
- `core/components/lighting/lightp/light.py:13-90,131-182`
