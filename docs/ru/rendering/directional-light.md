# Направленный свет
> Бесконечный свет-солнце: `DirectionalLight` (`core/components/lighting/lightp/light.py:185-225`) с интенсивностью в люксах и опциональной связью с процедурным небом.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Направление — из трансформа сущности (`-forward` используют шейдеры неба/облаков). Интенсивность — освещённость в люксах: солнце `100000`, день `10000–25000`, пасмурно `1000–10000`, рассвет `400`, офис `500`, сумерки `10`, луна `0.2` (описание инспектора + `LUX_REFERENCE`, `light.py:40-48,198`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `color` | color3 | `[1.0, 1.0, 1.0]` | Цвет (`light.py:197`). |
| `intensity` | float люксы (0–200000) | `100000.0` | Освещённость; шкала выше (`light.py:198,205`). |
| `procedural_sky_lighting` | bool | `False` | Связь с солнцем процедурного неба (`light.py:199,206`). |
| `cast_shadows` | bool | `True` | Вклад CSM; нужен активный `DirectionalShadow` (`light.py:201,207`). |
## Usage in Editor
Наведение — вращением сущности. Связывать с [Procedural Sky](./procedural-sky.md) через `procedural_sky_lighting` или `sun_light_entity_id`, CSM настраивать в [Shadows](./shadows.md).
## Usage in Code
```python
sun.intensity = 15000.0  # полный день
sun.procedural_sky_lighting = True
```
## Related pages
- [Light](./light.md), [Shadows](./shadows.md), [Procedural Sky](./procedural-sky.md), [Atmosphere](./atmosphere.md)
## Verified against
- `core/components/lighting/lightp/light.py:185-225`
