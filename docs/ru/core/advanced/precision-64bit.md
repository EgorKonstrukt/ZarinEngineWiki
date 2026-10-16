# Точность и 64 бита

> Мировая математика — везде `float64` (`FLOAT_TYPE = np.float64`); понижение до `float32` происходит только на границах загрузки в GPU (`to_f32` / `batch_to_f32` / `mat4_to_f32_col_major`). `FLT_EPSILON = 1.1920928955078125e-07` — эпсилон float32 для кода, живущего на обеих точностях.

[Home](../../../index.md) | [Section index](./_section.md)

## Обзор

`core/maths/math3d.py` задаёт `FLOAT_TYPE = np.float64`; `Mat4._d` — всегда массив `(4,4)` `float64`, и все конструкторы (`identity`, `translation`, `scale`, `perspective`, `orthographic`, `look_at`) строят во `float64`. Алгебра `Mat4` (`__mul__`, `transposed`, `inverted` через `np.linalg.inv`, `decompose`) остаётся во `float64`, поэтому трансляции больших миров сохраняют субмиллиметровое разрешение вдали от начала координат вместо джиттера.

`float32` появляется только когда данные покидают CPU-сторону:

- `Mat4.to_f32()` → плоская 16-float копия `float32` row-major для одиночной загрузки.
- `Mat4.batch_to_f32(matrices)` → `(n, 16)` `float32` через `batch_matrices_to_f32` (пустой вход → нули `(0, 16)`).
- `core/math_helpers.py`: `mat4_to_f32_col_major` (16 float column-major), `mat4_normal_matrix` (обратная-транспонированная как `float32` 3×3).
- Пейлоад-массивы `SharedPhysicsBuffer` — `float32` по построению (межпроцессная shared-память); входы/выходы физики квантуются там, а не в состоянии сцены.

`FLT_EPSILON` экспортируется из `core.maths.math3d` для эпсилон-сравнений с данными из `float32`-источников.

## Свойства

| имя | тип | значение | описание |
|---|---|---|---|
| `FLOAT_TYPE` | dtype | `np.float64` | `math3d.py` и `math_helpers.py` |
| `Mat4._d` | `ndarray` | `(4,4)` float64 | Каноническое мировое представление |
| `Mat4.to_f32()` | метод | 16 × float32 | Одиночная загрузка в GPU |
| `Mat4.batch_to_f32` | static | `(n,16)` float32 | Батчевая загрузка в GPU |
| `FLT_EPSILON` | float | `1.1920928955078125e-07` | Эпсилон float32 |
| пейлоад shared-буфера | dtype | float32 | Только транспорт физики через SHM |

## Использование в редакторе

Переключателя в редакторе нет: точность архитектурная. Большие сцены (оффсеты в тысячи) стоят стабильно во вьюпорте, потому что видовая/проекционная математика считается во `float64` до единственной загрузки `float32`.

## Использование в коде

```python
import numpy as np
from core.maths.math3d import Mat4, Vec3, FLOAT_TYPE, FLT_EPSILON

m = Mat4.translation(Vec3(10000.0, 0.0, 0.0))  # float64, точно
assert m._d.dtype == FLOAT_TYPE

gpu = m.to_f32()                       # float32 только для загрузки
batch = Mat4.batch_to_f32([m, Mat4()])  # (2, 16) float32
print(FLT_EPSILON)                      # 1.1920928955078125e-07
```

## Сериализация

Файлы сцен хранят float полной точности (JSON-числа); при save/load точность не теряется — квантование происходит только в транспортных копиях GPU/shared-памяти.

## Связанные страницы

- [Математика: Vec, Quat, Mat4, Curve](../../math/overview.md)
- [Math-хелперы](./math-helpers.md)
- [Трансформ](../transform.md)

## Проверено по

- `core/maths/math3d.py` (`FLOAT_TYPE`, `Mat4`, `to_f32`, `batch_to_f32`, `FLT_EPSILON`)
- `core/math_helpers.py` (`FLOAT_TYPE`, `mat4_to_f32_col_major`, `mat4_normal_matrix`, `batch_matrices_to_f32`)
- `core/physics/shared_buffer.py` (пейлоад-массивы `_DTYPE_F32`)
