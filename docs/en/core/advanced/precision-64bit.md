# Precision and 64-bit

> World math is `float64` everywhere (`FLOAT_TYPE = np.float64`); the downgrade to `float32` happens only at GPU-upload boundaries (`to_f32` / `batch_to_f32` / `mat4_to_f32_col_major`). `FLT_EPSILON = 1.1920928955078125e-07` marks the float32 epsilon for code that straddles both precisions.

[Home](../../../index.md) | [Section index](./_section.md)

## Overview

`core/maths/math3d.py` defines `FLOAT_TYPE = np.float64`; `Mat4._d` is always a `(4,4)` `float64` array and every constructor (`identity`, `translation`, `scale`, `perspective`, `orthographic`, `look_at`) builds in `float64`. `Mat4` algebra (`__mul__`, `transposed`, `inverted` via `np.linalg.inv`, `decompose`) stays in `float64`, so large-world translations keep sub-millimeter resolution far from the origin instead of jittering.

`float32` appears only when data leaves the CPU side:

- `Mat4.to_f32()` → flat 16-float `float32` row-major copy for a single upload.
- `Mat4.batch_to_f32(matrices)` → `(n, 16)` `float32` via `batch_matrices_to_f32` (empty input → `(0, 16)` zeros).
- `core/math_helpers.py`: `mat4_to_f32_col_major` (16-float column-major), `mat4_normal_matrix` (inverse-transpose as `float32` 3×3).
- `SharedPhysicsBuffer` payload arrays are `float32` by design (cross-process shared memory); physics inputs/outputs quantize there, not in scene state.

`FLT_EPSILON` is exported from `core.maths.math3d` for epsilon comparisons against `float32`-sourced data.

## Properties

| name | type | value | description |
|---|---|---|---|
| `FLOAT_TYPE` | dtype | `np.float64` | `math3d.py` and `math_helpers.py` |
| `Mat4._d` | `ndarray` | `(4,4)` float64 | Canonical world representation |
| `Mat4.to_f32()` | method | 16 × float32 | Single-matrix GPU upload |
| `Mat4.batch_to_f32` | static | `(n,16)` float32 | Batched GPU upload |
| `FLT_EPSILON` | float | `1.1920928955078125e-07` | float32 epsilon |
| shared-buffer payload | dtype | float32 | Physics SHM transport only |

## Usage in Editor

No editor switch: precision is architectural. Large-world scenes (offsets in the thousands) stay stable in the viewport because view/projection math is computed in `float64` before the single `float32` upload.

## Usage in Code

```python
import numpy as np
from core.maths.math3d import Mat4, Vec3, FLOAT_TYPE, FLT_EPSILON

m = Mat4.translation(Vec3(10000.0, 0.0, 0.0))  # float64, exact
assert m._d.dtype == FLOAT_TYPE

gpu = m.to_f32()                       # float32 only for upload
batch = Mat4.batch_to_f32([m, Mat4()])  # (2, 16) float32
print(FLT_EPSILON)                      # 1.1920928955078125e-07
```

## Serialization

Scene files store full-precision floats (JSON numbers); no precision is lost on save/load — quantization happens only in GPU/shared-memory transport copies.

## Related pages

- [Math: Vec, Quat, Mat4, Curve](../../math/overview.md)
- [Math Helpers](./math-helpers.md)
- [Transform](../transform.md)

## Verified against

- `core/maths/math3d.py` (`FLOAT_TYPE`, `Mat4`, `to_f32`, `batch_to_f32`, `FLT_EPSILON`)
- `core/math_helpers.py` (`FLOAT_TYPE`, `mat4_to_f32_col_major`, `mat4_normal_matrix`, `batch_matrices_to_f32`)
- `core/physics/shared_buffer.py` (`_DTYPE_F32` payload arrays)
