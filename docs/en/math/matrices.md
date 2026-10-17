# Matrices

`Mat4` (`core/maths/math3d.py:23-105`) is a row-major `(4,4)` `float64` matrix (`FLOAT_TYPE = np.float64`). Translation lives in row 3 (`m[3,0..2]`).

## Constructors

| Signature | Units / notes |
|---|---|
| `Mat4(data=None)` | `float64` copy; `None` → identity |
| `Mat4.identity()` | — |
| `Mat4.translation(v: Vec3)` | writes `v` into row 3 |
| `Mat4.scale(v: Vec3)` | diagonal `v.x/v.y/v.z` |
| `Mat4.perspective(fov_deg, aspect, near, far)` | `fov_deg` in **degrees** (`math.radians` inside) |
| `Mat4.orthographic(left, right, bottom, top, near, far)` | — |
| `Mat4.look_at(eye, center, up)` | `f=(center-eye).normalized()`, `r=f×up`, `u=r×f`; row-major view matrix |

## Algebra and access

| Member | Behaviour |
|---|---|
| `__mul__` / `__matmul__` | `Mat4(self._d @ o._d)` |
| `transposed()` | `Mat4(self._d.T)` |
| `inverted()` | `np.linalg.inv`; on `LinAlgError` returns identity (no epsilon threshold here — `1e-15` lives in `math_helpers.mat4_inv_fast`) |
| `to_array()` | `float64` copy |
| `to_f32()` | flat 16 `float32`, **row-major** (`reshape(-1)`) |
| `batch_to_f32(matrices)` | `(n,16)` `float32` via `batch_matrices_to_f32`; empty → `zeros((0,16))` |
| `to_list()` | nested Python lists |
| `get_translation()` | `Vec3(m[3,0], m[3,1], m[3,2])` |
| `decompose()` | `(pos, Quat, scale)`; scale via row norms, zero-guard `1e-10`, rotation from `rm.T` |

Helpers: `mat4_to_f32_col_major(m)` → 16 `float32` **column-major** (GPU upload; opposite layout to `to_f32`). `mat4_normal_matrix(model)` → inverse-transpose `float32` 3×3 (singular → identity 3×3).

## Examples

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

## Related

- [Math overview](overview.md)
- [Vectors](vectors.md), [Transforms](transforms.md)
- [Math Helpers](../core/advanced/math-helpers.md), [Precision and 64-bit](../core/advanced/precision-64bit.md)

## Verified against

- `core/maths/math3d.py:23-105` (`Mat4`), `:108` (`FLT_EPSILON`), `:15` (`FLOAT_TYPE`)
- `core/math_helpers.py:27-57` (`mat4_inv_fast`, `|det| < 1e-15`), `:226-232` (`mat4_to_f32_col_major`), `:368-374` (`mat4_normal_matrix`), `:377-381` (`batch_matrices_to_f32`)
