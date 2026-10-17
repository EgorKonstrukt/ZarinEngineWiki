# Системы координат

Только подтверждённые кодом соглашения. Остальное помечено как неизвестное.

## Подтверждено

| Соглашение | Доказательство |
|---|---|
| Y-up | `Vec3.up()` = `(0,1,0)`; экспорт PhysX USDA пишет `upAxis = "Y"`; импортёр BSP документирует `"Source (Z-up) -> ZarPy (Y-up)"` |
| Вперёд — −Z | `Vec3.forward()` = `(0,0,-1)`; `Transform.forward` = `−`строка 2 мировой матрицы |
| Вправо — +X | `Vec3.right()` = `(1,0,0)`; `Transform.right` = строка 0 |
| 1 юнит = 1 метр (экспорт физики) | USD-экспорт пишет `metersPerUnit = 1` |
| Rotation в инспекторе — градусы | поле `Rotation` правит `local_euler_angles`, который идёт через градусные `Quat.from_euler` / `Quat.to_euler` |
| API поворота в коде принимают градусы… | `Quat.from_euler(x,y,z)`, `Quat.from_axis_angle(axis, angle_deg)`, `Transform.rotate(euler)`, `Mat4.perspective(fov_deg, ...)` |
| …кроме явных `_rad` / `fov_rad` вариантов | `quat_from_euler_rad`, `quat_to_euler_rad`, `mat4_perspective(fov_rad, ...)` — радианы |

## Не подтверждено кодом

- Глобальное «мир в метрах» за пределами экспорта физики (`metersPerUnit = 1` в `solverp/scenes.py:63` — строка экспорта, не константа ядра).
- Соответствие лево/право GUI осям мира — код не найден; здесь не документируется.
- Z-up или леворукий режим в рантайме: хелперы `coordinate_system_axes` / `is_right_handed_cs` существуют для конвертаций, но рантайм — Y-up праворукий (см. выше).

## Пример

```python
from core.maths.math3d import Vec3, Quat

assert Vec3.up().to_list() == [0.0, 1.0, 0.0]
assert Vec3.forward().to_list() == [0.0, 0.0, -1.0]

q_deg = Quat.from_euler(0.0, 90.0, 0.0)   # градусы
print(q_deg.to_euler().to_list())         # обратно градусы

from core import math_helpers as mh
q_rad = mh.quat_from_euler_rad(0.0, 1.5707963, 0.0)  # радианный вариант
```

## Связанные страницы

- [Обзор математики](overview.md)
- [Векторы](vectors.md), [Кватернионы](quaternions.md), [Трансформы](transforms.md)
- [Точность и 64 бит](../core/advanced/precision-64bit.md)

## Проверено по

- `core/pyx/_math_vec.pyx:261-283` (`up`/`forward`/`right`)
- `core/components/transform/tr/matrix.py:281-318` (`forward`/`right`/`up`)
- `core/components/transform/tr/component.py:20` (поле Rotation), `:108-116` (градусный round-trip)
- `physics_solvers/physx_solver/solverp/scenes.py:63-64` (`metersPerUnit = 1`, `upAxis = "Y"`)
- `plugins/user/bsp_import/build.py:66` (пометка Z-up → Y-up)
- `core/math_helpers.py:122-165` (градусы против радиан), `core/maths/math3d.py:42-44` (`fov_deg`)
