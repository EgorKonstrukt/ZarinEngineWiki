# Трансформы

Математика трансформов (`core/components/transform/tr/matrix.py:23-390`): TRS-композиция в row-major `float64` — каждая строка поворота масштабируется (`out[0,:3] = sx*R[0]` …), трансляция в строке 3, мировые матрицы цепочкой `world = local @ parent_world`.

## Градусы и радианы

`Transform.rotate(euler: Vec3)` и `local_euler_angles` — **градусы**: `rotate` внутри применяет `math.radians` и домножает локальный кватернион; геттер/сеттер идут через `Quat.from_euler` / `Quat.to_euler` (оба в градусах). Радианные варианты есть только в `math_helpers` (`quat_from_euler_rad`, `mat4_perspective(fov_rad, ...)`).

## Направления

| Член | Определение |
|---|---|
| `forward` | `−`строка 2 мировой матрицы, нормализовано (порог `1e-10`, иначе ноль) → дефолт `(0,0,-1)` |
| `right` | строка 0, нормализовано |
| `up` | строка 1, нормализовано |
| `look_at(target, up=None)` | `up` по умолчанию `Vec3.up()`; ставит локальный поворот через `Quat.look_rotation((target-position).normalized(), up)` |

## Локальная и мировая трансляция

`translate(delta, world_space=False)`: по умолчанию добавляет к локальной позиции; `world_space=True` добавляет к мировому свойству `position` (через обратную матрицу родителя).

## Примеры

```python
from core.maths.math3d import Vec3

t = entity.transform
t.translate(Vec3(0.0, 0.0, 5.0))             # локальный сдвиг
t.translate(Vec3(0.0, 1.0, 0.0), world_space=True)  # сдвиг в мировых координатах
t.rotate(Vec3(0.0, 45.0, 0.0))               # градусы, домножение справа
t.look_at(Vec3(0.0, 1.0, 0.0))               # up по умолчанию Vec3.up()
print(t.forward.to_list())                   # взгляд в −Z
print(t.local_euler_angles.to_list())        # градусы

# Орбитальная камера (кадрово-независимо через dt):
import math
angle = 0.0
def on_update(dt):
    global angle
    angle += 20.0 * dt                        # 20 град/сек
    r = 8.0
    a = math.radians(angle)
    t.position = Vec3(math.sin(a) * r, 3.0, math.cos(a) * r)
    t.look_at(Vec3(0.0, 1.0, 0.0))
```

## Связанные страницы

- [Обзор математики](overview.md)
- [Векторы](vectors.md), [Кватернионы](quaternions.md), [Матрицы](matrices.md), [Координаты](coordinates.md)
- [Curve API](../core/advanced/curve-api.md)
- [Input и Math API](../scripting/input-math-api.md)

## Проверено по

- `core/components/transform/tr/matrix.py:23-72` (`_write_local_into`, строки TRS), `:281-318` (`forward`/`right`/`up`), `:319-354` (`translate`), `:355-384` (`rotate`, градусы), `:385-390` (`look_at`)
- `core/components/transform/tr/component.py:108-116` (`local_euler_angles`, градусы)
- `core/pyx/_math_vec.pyx:261-283` (`up`/`forward`/`right`)
