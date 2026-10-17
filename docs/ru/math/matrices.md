# Матрицы

`Mat4` (`core/maths/math3d.py:23-105`) — row-major матрица `(4,4)` `float64` (`FLOAT_TYPE = np.float64`). Трансляция — в строке 3 (`m[3,0..2]`).

## Конструкторы

| Сигнатура | Единицы / примечания |
|---|---|
| `Mat4(data=None)` | копия `float64`; `None` → единичная |
| `Mat4.identity()` | — |
| `Mat4.translation(v: Vec3)` | пишет `v` в строку 3 |
| `Mat4.scale(v: Vec3)` | диагональ `v.x/v.y/v.z` |
| `Mat4.perspective(fov_deg, aspect, near, far)` | `fov_deg` в **градусах** (`math.radians` внутри) |
| `Mat4.orthographic(left, right, bottom, top, near, far)` | — |
| `Mat4.look_at(eye, center, up)` | `f=(center-eye).normalized()`, `r=f×up`, `u=r×f`; row-major view-матрица |

## Алгебра и доступ

| Член | Поведение |
|---|---|
| `__mul__` / `__matmul__` | `Mat4(self._d @ o._d)` |
| `transposed()` | `Mat4(self._d.T)` |
| `inverted()` | `np.linalg.inv`; при `LinAlgError` — единичная (явного порога тут нет — `1e-15` живёт в `math_helpers.mat4_inv_fast`) |
| `to_array()` | копия `float64` |
| `to_f32()` | плоские 16 `float32`, **row-major** (`reshape(-1)`) |
| `batch_to_f32(matrices)` | `(n,16)` `float32` через `batch_matrices_to_f32`; пустой вход → `zeros((0,16))` |
| `to_list()` | вложенные списки Python |
| `get_translation()` | `Vec3(m[3,0], m[3,1], m[3,2])` |
| `decompose()` | `(pos, Quat, scale)`; масштаб через нормы строк, защита от нуля `1e-10`, поворот из `rm.T` |

Хелперы: `mat4_to_f32_col_major(m)` → 16 `float32` **column-major** (загрузка в GPU; раскладка противоположна `to_f32`). `mat4_normal_matrix(model)` → обратная транспонированная `float32` 3×3 (вырожденная → единичная 3×3).

## Примеры

```python
from core.maths.math3d import Mat4, Vec3

model = Mat4.translation(Vec3(1.0, 2.0, 3.0)) @ Mat4.scale(Vec3(2.0, 2.0, 2.0))
pos, rot, scl = model.decompose()
print(pos.to_list())  # [1.0, 2.0, 3.0]
print(scl.to_list())  # [2.0, 2.0, 2.0]

view = Mat4.look_at(Vec3(0, 0, 5), Vec3(0, 0, 0), Vec3.up())
proj = Mat4.perspective(60.0, 16.0 / 9.0, 0.1, 1000.0)
print(view.get_translation().to_list())

gpu = Mat4.batch_to_f32([model, Mat4.identity()])  # (2, 16) float32
print(gpu.shape, gpu.dtype)

from core import math_helpers as mh
flat_col_major = mh.mat4_to_f32_col_major(model.to_array())  # 16 float32, column-major
normal = mh.mat4_normal_matrix(model.to_array())              # (3, 3) float32
```

## Связанные страницы

- [Обзор математики](overview.md)
- [Векторы](vectors.md), [Трансформы](transforms.md)
- [Math Helpers](../core/advanced/math-helpers.md), [Точность и 64 бит](../core/advanced/precision-64bit.md)

## Проверено по

- `core/maths/math3d.py:23-105` (`Mat4`), `:108` (`FLT_EPSILON`), `:15` (`FLOAT_TYPE`)
- `core/math_helpers.py:27-57` (`mat4_inv_fast`, `|det| < 1e-15`), `:226-232` (`mat4_to_f32_col_major`), `:368-374` (`mat4_normal_matrix`), `:377-381` (`batch_matrices_to_f32`)
