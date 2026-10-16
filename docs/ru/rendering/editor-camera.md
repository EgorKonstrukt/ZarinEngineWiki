# Камера редактора
> Полётная камера вьюпорта: `EditorCamera` (`core/components/rendering/cameras/editor_camera.py`); правая кнопка WASD/QE, средняя — пан, колесо — долли.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Вращение правой кнопкой или Alt+левой (`rotate_speed`), пан средней кнопкой, долли колесом (`zoom_speed` с акселерацией скролла). `on_update` летит на W/A/S/D + Q/E с разгоном (`FAST_MULT = 3.0`, `SPEED_BOOST_RAMP = 2.0`), accel `12.0`, damping `8.0` (`editor_camera.py:22-29,136-175`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `move_speed` | float (0.1–100.0) | `5.0` | Базовая скорость (`editor_camera.py:35,47`). |
| `rotate_speed` | float (0.01–5.0) | `0.3` | Чувствительность обзора (`editor_camera.py:36,48`). |
| `zoom_speed` | float (0.1–50.0) | `4.0` | Чувствительность колеса (`editor_camera.py:37,49`). |
| `pan_speed` | float (0.001–0.1) | `0.01` | Чувствительность пана (`editor_camera.py:38,50`). |
| `damping` | float (0.1–50.0) | `8.0` | Сглаживание (`editor_camera.py:39,51`). |
| `acceleration` | float (1.0–100.0) | `12.0` | Ускорение полёта (`editor_camera.py:40,52`). |
## Usage in Editor
Держать правую кнопку + WASD для полёта, Q/E вниз/вверх; после отпускания — затухающий дрейф. Средний drag — пан; колесо — долли. Yaw/pitch сохраняются в `serialize`.
## Usage in Code
```python
ec = entity.get_component(EditorCamera)
ec.move_speed = 8.0
ec.rotate_speed = 0.25
```
## Related pages
- [Camera](./camera.md), [Gizmos](./gizmos.md)
## Verified against
- `core/components/rendering/cameras/editor_camera.py:22-60,95-175`
