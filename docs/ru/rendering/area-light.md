# Прямоугольный свет
> Панельный свет: `AreaLight` (`core/components/lighting/lightp/shadows.py:79-185`) с яркостью в нитах, формой rect/disk и мультисэмпл-тенями.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Яркость поверхности в нитах (кд/м²): монитор `200`, офисная панель `500`, пасмурное небо `2000`, софтбокс `10000`. Те же ниты на большей панели дают больше света (`shadows.py:92`). Гизмо рисует рамку, опциональное кольцо диска и заднюю рамку двусторонних (`shadows.py:118-161`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `color` | color3 | `[1.0, 1.0, 1.0]` | Цвет (`shadows.py:91`). |
| `intensity` | float ниты (0–200000) | `100.0` | Яркость поверхности (`shadows.py:92,106`). |
| `range` | float (0–10000) | `10.0` | Дистанция отсечения (`shadows.py:93,107`). |
| `area_type` | `LightAreaType` | `RECT` | `rect` / `disk` (`shadows.py:95,108`). |
| `area_width` | float (0.01–100) | `1.0` | Ширина панели, м (`shadows.py:96,109`). |
| `area_height` | float (0.01–100) | `1.0` | Высота панели, м (`shadows.py:97,110`). |
| `area_double_sided` | bool | `False` | Излучение с двух сторон (`shadows.py:98,111`). |
| `area_samples` | int (1–64) | `6` | Сэмплы теней (`shadows.py:99,112`). |
| `area_shadow_bias` | float (0–0.1) | `0.005` | Биас теней (`shadows.py:100,113`). |
| `cast_shadows` | bool | `True` | Нужен активный `AreaShadow` (`shadows.py:102,114`). |
## Usage in Editor
Масштаб — через Area Width/Height, не трансформ. Disk для круглых софтбоксов; Double Sided для фонарей. Samples выше — мягче, Bias — от акне.
## Usage in Code
```python
al.area_type = LightAreaType.DISK
al.area_width = 2.0
al.area_samples = 12
```
## Related pages
- [Light](./light.md), [Shadows](./shadows.md)
## Verified against
- `core/components/lighting/lightp/shadows.py:79-185`
- `core/components/lighting/lightp/light.py:20-22` (`LightAreaType`)
