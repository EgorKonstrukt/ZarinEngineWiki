# Векторы

`Vec2`, `Vec3`, `Vec4` — Cython-типы, собранные из `core/pyx/_math_vec.pyx` и реэкспортированные через `core/maths/math3d.py`. Все компоненты — `float64`.

## Конструкторы

| Тип | Сигнатура | Дефолты | Примечания |
|---|---|---|---|
| `Vec2` | `Vec2(x=0.0, y=0.0)` | `0.0, 0.0` | у `x`/`y` есть сеттеры |
| `Vec3` | `Vec3(x=0.0, y=0.0, z=0.0)` | `0.0, 0.0, 0.0` | сеттеры; `__getitem__` 0–2 (`IndexError` иначе); итерируемый |
| `Vec4` | `Vec4(x=0.0, y=0.0, z=0.0, w=1.0)` | обратите внимание `w=1.0` | `x`/`y`/`z`/`w` — **только чтение** (сеттеров нет) |
| `from_array` | `Vec2.from_array(a)` / `Vec3.from_array(a)` / `Vec4.from_array(a)` | — | `float(a[i])` покомпонентно |

Статические конструкторы есть только у `Vec2`/`Vec3`: `zero()`, `one()`. У `Vec3` дополнительно `up()` → `(0,1,0)`, `forward()` → `(0,0,-1)`, `right()` → `(1,0,0)`. У `Vec4` шорткатов нет.

## Операторы (покомпонентно, скаляр вторым операндом)

| Метод | `Vec2` | `Vec3` | `Vec4` |
|---|---|---|---|
| `+`, `-` (вектор) | да | да | **нет** |
| `*`, `__rmul__`, `/` (скаляр) | да | да | **нет** |
| унарный `-` | да | да | **нет** |
| `dot(o)` | да | да | **нет** |
| `cross(o)` | **нет** | да | **нет** |
| `length()` | да | да | **нет** |
| `length_sq()` | **нет** | да | **нет** |
| `distance_to(o)` / `distance_sq_to(o)` | **нет** | да | **нет** |
| `normalized()` | да | да | **нет** |
| `lerp(o, t)` | **нет** | да | **нет** |
| `to_list()` | да | да | да |
| `to_array()` (`float64`) | **нет** | да | **нет** |

Пороги: `normalized()` возвращает нулевой вектор при длине `<= 1e-10`. `__eq__` — допуск `1e-8` покомпонентно. `__repr__` — 4 знака (`Vec3(1.0000, ...)`).

Отсутствует в коде: метода `Vec3.rotate()` нет — поворот через `Quat.rotate_vec3` или `math3d.rotate_around_axis_flat`. У `Vec2` нет `lerp`, `cross`, `distance_to`; у `Vec4` вообще нет арифметики.

## Примеры

```python
from core.maths.math3d import Vec2, Vec3, Vec4

a = Vec3(1.0, 0.0, 0.0)
b = Vec3(0.0, 1.0, 0.0)
print(a.cross(b).to_list())   # [0.0, 0.0, 1.0]
print(a.dot(b))               # 0.0
print(a.lerp(b, 0.5).to_list())  # [0.5, 0.5, 0.0]

print(Vec3.up().to_list())      # [0.0, 1.0, 0.0]
print(Vec3.forward().to_list()) # [0.0, 0.0, -1.0]

v = Vec3(3.0, 0.0, 4.0)
print(v.length(), v.length_sq())  # 5.0 25.0
print(v.normalized().to_list())   # [0.6, 0.0, 0.8]
print(Vec3().normalized().to_list())  # [0.0, 0.0, 0.0] (порог 1e-10)

p = Vec4(1.0, 2.0, 3.0)  # w по умолчанию 1.0
print(p.to_list())       # [1.0, 2.0, 3.0, 1.0]
```

## Связанные страницы

- [Обзор математики](overview.md)
- [Матрицы](matrices.md), [Кватернионы](quaternions.md)
- [Curve API](../core/advanced/curve-api.md)
- [Math Helpers](../core/advanced/math-helpers.md), [Точность и 64 бит](../core/advanced/precision-64bit.md)
- [Input и Math API](../scripting/input-math-api.md)

## Проверено по

- `core/pyx/_math_vec.pyx:17-109` (`Vec2`), `:111-287` (`Vec3`), `:289-333` (`Vec4`)
- `core/maths/math3d.py:11` (реэкспорт), `:15` (`FLOAT_TYPE`)
