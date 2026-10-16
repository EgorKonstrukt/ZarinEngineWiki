# Math Helpers

> `core/math_helpers.py` is the numeric layer: `float64` functions on raw scalars/`ndarray`s for mat4, quaternion, vec3 and ray casts. It is the pure-Python fallback — the built Cython `math_helpers.pyx` shadows it at import for speed.

[Home](../../../index.md) | [Section index](./_section.md)

## Overview

`FLOAT_TYPE = np.float64` everywhere in this module. All functions take and return plain floats/tuples/`ndarray`s — no `Vec`/`Mat4` objects — so they suit hot loops and batch code:

- mat4: `mat4_mul_fast(a, b)`, `mat4_inv_fast(m)` (adjugate; singular `|det| < 1e-15` → identity), `mat4_translation`, `mat4_scale_mat`, `mat4_from_quaternion`, `mat4_to_f32_col_major` (16-float `float32` column-major for GPU upload), `mat4_mul_vec3`, `mat4_look_at`, `mat4_perspective`, `mat3x3_inv`, `mat4_normal_matrix` (inverse-transpose as `float32` 3×3), `batch_matrices_to_f32` (`(n,4,4)` → `(n,16)` `float32`).
- quat: `quat_conjugate`, `quat_mul`, `quat_slerp` (with sign-flip and lerp fallback past `dot > 0.9995`), `quat_normalize`, `quat_rotate_vec3`, `quat_from_euler` / `quat_to_euler` (degrees), `quat_from_euler_rad` / `quat_to_euler_rad`.
- vec3: `vec3_normalize` (zero → `(0,0,0)`), `vec3_sub` / `add` / `scale` / `dot` / `cross`.
- ray casts (Möller–Trumbore family, `-1.0` on miss): `ray_triangle_intersect` (epsilon `1e-12`), `ray_mesh_intersect` (closest hit over index triplets), `ray_sphere_intersect_batch` (struct-of-arrays batch), `ray_aabb_intersect` (slab test).

Use these instead of `Vec`/`Mat4` when operating on raw buffers, per-frame batches (skin/cull/raycast loops), or solver-side code that must avoid object overhead. Use `Vec2/3/4`, `Quat`, `Mat4` for component state, inspector-exposed values and scene serialization. The float64→float32 downgrade happens only at upload boundaries (`to_f32`, `batch_to_f32`, `mat4_to_f32_col_major`, `mat4_normal_matrix`).

## Properties

| function group | members | notes |
|---|---|---|
| mat4 build | `mat4_translation`, `mat4_scale_mat`, `mat4_from_quaternion`, `mat4_look_at`, `mat4_perspective` | Row-major `float64`, translation in row 3 |
| mat4 algebra | `mat4_mul_fast`, `mat4_inv_fast`, `mat3x3_inv`, `mat4_mul_vec3` | Explicit unrolled loops |
| quat | `quat_mul`, `quat_slerp`, `quat_conjugate`, `quat_normalize`, `quat_rotate_vec3`, euler ×4 | Degrees and radians variants |
| vec3 | `vec3_normalize/sub/add/scale/dot/cross` | Scalar-tuple API |
| ray | `ray_triangle_intersect`, `ray_mesh_intersect`, `ray_sphere_intersect_batch`, `ray_aabb_intersect` | `-1.0` on miss |
| GPU upload | `mat4_to_f32_col_major`, `mat4_normal_matrix`, `batch_matrices_to_f32` | `float32` output only here |

## Usage in Editor

No direct editor surface; these helpers back gizmo math, mesh import normals, BVH/culling batches and physics-side transforms. Editor-visible behavior (normals, raycast picking) goes through them transparently.

## Usage in Code

```python
import numpy as np
from core import math_helpers as mh

m = mh.mat4_translation(1.0, 2.0, 3.0)
inv = mh.mat4_inv_fast(m)
print(mh.mat4_mul_vec3(m, 0.0, 0.0, 0.0))  # (1.0, 2.0, 3.0)

q = mh.quat_from_euler(0.0, 90.0, 0.0)
print(mh.quat_rotate_vec3(*q, 1.0, 0.0, 0.0))

t = mh.ray_triangle_intersect(0, 0, 5, 0, 0, -1,
                              -1, -1, 0, 1, -1, 0, 0, 1, 0)
print(t)  # 5.0, -1.0 on miss

gpu = mh.batch_matrices_to_f32(np.stack([m, inv]))  # (2, 16) float32
```

## Serialization

Nothing here serializes; outputs are transient numerics. Persist `Mat4`/`Quat` via their own `to_list`/`to_dict` forms instead.

## Related pages

- [Math: Vec, Quat, Mat4, Curve](../../math/overview.md)
- [Precision and 64-bit](./precision-64bit.md)
- [Spatial](./spatial.md)
- [Threading](./threading.md)

## Verified against

- `core/math_helpers.py` (all function names, thresholds, `FLOAT_TYPE`, fallback comment)
- `core/pyx/math_helpers.pyx` (Cython shadow module)
- `core/maths/math3d.py` (`to_f32`, `batch_to_f32`, `FLT_EPSILON`)
