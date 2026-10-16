# Прожектор
> Конусный свет: `SpotLight` (`core/components/lighting/lightp/light.py:304-381`) с люменами, дальностью и внешним/внутренним углами.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Те же люмены, что у point, но сконцентрированы в конусе: узкий конус ярче широкого при равных люменах (описание инспектора, `light.py:317`). Гизмо рисует круг основания + 8 рёбер (`light.py:335-364`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `color` | color3 | `[1.0, 1.0, 1.0]` | Цвет (`light.py:316`). |
| `intensity` | float люмены (0–200000) | `1000.0` | Поток внутри конуса (`light.py:317,327`). |
| `range` | float (0–10000) | `10.0` | Длина конуса (`light.py:318,328`). |
| `spot_angle` | float (1–179) | `30.0` | Внешний конус, градусы (`light.py:320,329`). |
| `spot_inner_angle` | float (0–179) | `20.0` | Начало спада, градусы (`light.py:321,330`). |
| `cast_shadows` | bool | `True` | Перспективная карта; нужен `SpotShadow` (`light.py:323,331`). |
## Usage in Editor
Наведение транформом; держать `spot_inner_angle < spot_angle` для мягкого края. Фонарик `100–1000` лм, улица `1000–40000` лм.
## Usage in Code
```python
sp.spot_angle = 45.0
sp.spot_inner_angle = 30.0
sp.range = 25.0
```
## Related pages
- [Light](./light.md), [Point Light](./point-light.md), [Projector](./projector.md), [Shadows](./shadows.md)
## Verified against
- `core/components/lighting/lightp/light.py:304-381`
