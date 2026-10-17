# Кватернионы

`Quat` (`core/pyx/_math_vec.pyx:336-470`) — `(x, y, z, w)` `float64`, дефолт — identity `(0,0,0,1)`. Компоненты — свойства только для чтения. Тяжёлые операции делегированы `core.math_helpers` (`quat_*`).

## Создание

| Сигнатура | Единицы / примечания |
|---|---|
| `Quat(x=0, y=0, z=0, w=1)` | — |
| `Quat.from_array(a)` | 4 числа |
| `Quat.identity()` | `(0,0,0,1)` |
| `Quat.from_euler(x, y, z)` | **градусы**, через `quat_from_euler` |
| `Quat.from_axis_angle(axis, angle_deg)` | ось автонормализуется; `angle_deg` в **градусах** (`math.radians(angle) * 0.5`) |
| `Quat.look_rotation(forward, up=None)` | `up` по умолчанию `Vec3.up()`; базис `r = up×f`, `u = f×r` |

Метода `Quat.from_euler_rad` нет — радианные варианты (`quat_from_euler_rad`) существуют только как функции модуля `math_helpers`.

## Операции

| Метод | Поведение |
|---|---|
| `__mul__(o)` | произведение Гамильтона через `quat_mul` |
| `conjugate()` | `(-x,-y,-z,w)` |
| `normalized()` | порог `n > 1e-10`, иначе путь к identity |
| `rotate_vec3(v)` | поворачивает `Vec3`, возвращает `Vec3` |
| `to_euler()` | возвращает `Vec3` в **градусах** через `quat_to_euler` |
| `to_matrix4()` | сначала нормализует, возвращает `Mat4` |
| `slerp(o, t)` | через `quat_slerp` + перенормировка (порог `1e-10`, иначе identity) |
| `to_list()` | `[x, y, z, w]` |

Детали `slerp` (`math_helpers`): отражение знака при `dot < 0`, запасной lerp при `dot > 0.9995` (затем нормализация). Метода `lerp` у `Quat` нет.

## Примеры

```python
from core.maths.math3d import Quat, Vec3

q = Quat.from_euler(0.0, 90.0, 0.0)          # градусы!
print(q.rotate_vec3(Vec3(1, 0, 0)).to_list())  # ~[0.0, 0.0, -1.0]

spin = Quat.from_axis_angle(Vec3.up(), 45.0)   # 45 градусов вокруг Y
print(spin.to_euler().to_list())               # [0.0, 45.0, 0.0] (градусы)

a = Quat.identity()
b = Quat.from_euler(0.0, 90.0, 0.0)
mid = a.slerp(b, 0.5)
print(mid.rotate_vec3(Vec3.forward()).to_list())

m = b.to_matrix4()  # Mat4-матрица поворота
```

## Связанные страницы

- [Обзор математики](overview.md)
- [Векторы](vectors.md), [Матрицы](matrices.md), [Трансформы](transforms.md)
- [Math Helpers](../core/advanced/math-helpers.md)
- [Input и Math API](../scripting/input-math-api.md)

## Проверено по

- `core/pyx/_math_vec.pyx:336-470` (`Quat`)
- `core/math_helpers.py:88-94` (`quat_mul`), `:97-119` (`quat_slerp`, `0.9995`), `:122-144` (`quat_from_euler`/`quat_to_euler`, градусы), `:147-165` (радианные варианты), `:168-184` (normalize, rotate)
