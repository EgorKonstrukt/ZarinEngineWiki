# Точечный свет
> Всенаправленная лампа: `PointLight` (`core/components/lighting/lightp/light.py:228-301`) с интенсивностью в люменах и конечным радиусом.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Полный поток в люменах: свеча `12.57`, настольная лампа `300`, потолочная `800`, лампа 100Вт `1600`, уличная `15000`, стадион `100000` (`LUMEN_REFERENCE`, `light.py:49-57,241`). Гизмо рисует три круга радиуса и осевые риски (`light.py:254-286`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `color` | color3 | `[1.0, 1.0, 1.0]` | Цвет (`light.py:240`). |
| `intensity` | float люмены (0–200000) | `1000.0` | Полный поток (`light.py:241,248`). |
| `range` | float (0–10000) | `10.0` | Дистанция отсечения (`light.py:242,249`). |
| `cast_shadows` | bool | `True` | Куб-тени; нужен активный `PointShadow` (`light.py:244,250`). |
## Usage in Editor
Ставить на высоте лампы, Range — радиус комнаты, интенсивность — из таблицы люменов. Тени выключать у дешёвых заливок.
## Usage in Code
```python
pl.intensity = 800.0  # потолочная
pl.range = 12.0
```
## Related pages
- [Light](./light.md), [Spot Light](./spot-light.md), [Shadows](./shadows.md)
## Verified against
- `core/components/lighting/lightp/light.py:228-301`
