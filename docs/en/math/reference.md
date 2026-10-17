# Reference

One-line lookup for every math function. `CY` = Cython-compiled (`core._math_vec` / `core.math_helpers` `.pyd` shadowing the `.py`), `FB` = pure-Python fallback in `core/math_helpers.py` (its header comment states it is the fallback).

## `_math_vec` (`Vec`/`Quat` methods)

| Name | Signature | Description |
|---|---|---|
| `Vec2.__init__` | `(x=0.0, y=0.0)` | Constructor, settable `x`/`y` |
| `Vec2.from_array` | `(a)` | From 2-sequence |
| `Vec2.__add__/__sub__` | `(o)` | Component-wise add/sub |
| `Vec2.__mul__/__rmul__/__truediv__` | `(s)` | Scalar mul/div |
| `Vec2.__neg__` | `()` | Negation |
| `Vec2.dot` | `(o)` | Dot product |
| `Vec2.length` | `()` | Euclidean length |
| `Vec2.normalized` | `()` | Unit vector; zero if `l <= 1e-10` |
| `Vec2.to_list` | `()` | `[x, y]` |
| `Vec2.zero/one` | `()` | `(0,0)` / `(1,1)` |
| `Vec3.__init__` | `(x=0.0, y=0.0, z=0.0)` | Constructor, settable, indexable, iterable |
| `Vec3.from_array` | `(a)` | From 3-sequence |
| `Vec3.__add__/__sub__` | `(o)` | Component-wise add/sub |
| `Vec3.__mul__/__rmul__/__truediv__` | `(s)` | Scalar mul/div |
| `Vec3.__neg__` | `()` | Negation |
| `Vec3.dot/cross` | `(o)` | Dot / cross product |
| `Vec3.length/length_sq` | `()` | Length / squared length |
| `Vec3.distance_to/distance_sq_to` | `(o)` | Distance / squared distance |
| `Vec3.normalized` | `()` | Unit vector; zero if `l <= 1e-10` |
| `Vec3.lerp` | `(o, t)` | Linear interpolation, unclamped `t` |
| `Vec3.to_array` | `()` | `float64` ndarray |
| `Vec3.to_list` | `()` | `[x, y, z]` |
| `Vec3.zero/one/up/forward/right` | `()` | `(0,0,0)` / `(1,1,1)` / `(0,1,0)` / `(0,0,-1)` / `(1,0,0)` |
| `Vec4.__init__` | `(x=0.0, y=0.0, z=0.0, w=1.0)` | Constructor, read-only props |
| `Vec4.from_array` | `(a)` | From 4-sequence |
| `Vec4.to_list` | `()` | `[x, y, z, w]` |
| `Quat.__init__` | `(x=0.0, y=0.0, z=0.0, w=1.0)` | Constructor, read-only props |
| `Quat.from_array` | `(a)` | From 4-sequence |
| `Quat.identity` | `()` | `(0,0,0,1)` |
| `Quat.from_euler` | `(x, y, z)` | Degrees → quat |
| `Quat.from_axis_angle` | `(axis, angle_deg)` | Normalized-axis rotation, degrees |
| `Quat.look_rotation` | `(forward, up=None→Vec3.up())` | Basis → quat |
| `Quat.__mul__` | `(o)` | Hamilton product (CY `quat_mul`) |
| `Quat.conjugate` | `()` | `(-x,-y,-z,w)` |
| `Quat.normalized` | `()` | Unit quat (`1e-10` guard) |
| `Quat.rotate_vec3` | `(v)` | Rotated `Vec3` |
| `Quat.to_euler` | `()` | `Vec3` degrees |
| `Quat.to_matrix4` | `()` | Normalized → `Mat4` |
| `Quat.slerp` | `(o, t)` | Slerp, `0.9995` lerp fallback |

## `math_helpers` (CY + FB, scalar/`ndarray` API)

| Name | Signature | Description |
|---|---|---|
| `mat4_mul_fast` | `(a, b)` | 4×4 multiply, unrolled |
| `mat4_inv_fast` | `(m)` | Adjugate inverse; `|det|<1e-15` → identity |
| `mat4_translation` | `(pos_x, pos_y, pos_z)` | Translation matrix, row 3 |
| `mat4_scale_mat` | `(sx, sy, sz)` | Scale matrix |
| `mat4_from_quaternion` | `(x, y, z, w)` | Rotation matrix |
| `mat4_to_f32_col_major` | `(m)` | 16 `float32` column-major |
| `mat4_mul_vec3` | `(m, vx, vy, vz)` | Point transform (w=1) |
| `mat4_mul_vec_flat` | `(m, x, y, z, w=1.0)` | Flat 16-list point transform |
| `mat4_mul_flat` | `(l, r)` | Flat 16-list multiply |
| `mat4_look_at` | `(eye×3, center×3, up×3)` | View matrix |
| `mat4_perspective` | `(fov_rad, aspect, near, far)` | Projection, **radians** |
| `mat3x3_inv` | `(m00…m22)` | 3×3 inverse; `|det|<1e-15` → identity |
| `mat4_normal_matrix` | `(model)` | Inverse-transpose `float32` 3×3 |
| `batch_matrices_to_f32` | `(matrices (n,4,4) f64)` | `(n,16)` `float32` |
| `quat_conjugate/mul/normalize` | `(x,y,z,w)[×2]` | Basic quat ops |
| `quat_rotate_vec3` | `(qx…qw, vx, vy, vz)` | Rotate vector |
| `quat_slerp` | `(a×4, b×4, t)` | Slerp, `0.9995` fallback |
| `quat_from_euler/to_euler` | `(x,y,z)[,w]` | Degree variants |
| `quat_from_euler_rad/to_euler_rad` | `(x,y,z)[,w]` | Radian variants |
| `vec3_normalize/sub/add/scale/dot/cross` | scalars | Tuple-returning vec3 ops |
| `dot3_flat/cross3_flat/normalize3_flat` | scalars | Flat triplets (`1e-10` norm guard) |
| `rotate_around_axis_flat` | `(v×3, a×3, angle)` | Rodrigues rotation, **radians** |
| `axis_vector_flat` | `(axis, sign=1.0)` | Basis vector tuple |
| `coordinate_system_axes` | `(cs)` | Axis permutation tuple |
| `is_right_handed_cs` | `(cs)` | `cs in (0,1,2)` |
| `look_at_flat` | `(eye, at, up, cs)` | Flat view matrix with handedness |
| `mat4_invert_flat` | `(m)` | Flat adjugate inverse (no `1e-15` guard; raw `1/det`) |
| `point_in_circle` | `(cx, cy, radius, px, py)` | 2D containment |
| `ray_triangle_intersect` | `(o×3, d×3, v0×3, v1×3, v2×3)` | `t` or `-1.0`, `1e-12` |
| `ray_mesh_intersect` | `(o×3, d×3, verts, indices)` | Closest `t` or `-1.0` |
| `ray_aabb_intersect` | `(o×3, d×3, bmin×3, bmax×3)` | Slab `t` or `-1.0` |
| `ray_sphere_intersect_batch` | `(SoA arrays…, results)` | Batch sphere `t`/`-1.0` |

## `math3d` extras (NumPy/`Mat4` layer)

| Name | Signature | Description |
|---|---|---|
| `Mat4` constructors | `identity/translation/scale/perspective(fov_deg)/orthographic/look_at` | `float64` builders |
| `Mat4` algebra | `__mul__/__matmul__/transposed/inverted` | `inverted`: `LinAlgError` → identity |
| `Mat4` export | `to_array/to_f32/batch_to_f32/to_list/get_translation/decompose` | CPU→GPU boundary |
| `FLT_EPSILON` | `1.1920928955078125e-07` | float32 epsilon (in `math3d`, not `constants.py`) |
| `FLOAT_TYPE` | `np.float64` | Canonical dtype |

## Related

- [Math overview](overview.md)
- [Vectors](vectors.md), [Matrices](matrices.md), [Quaternions](quaternions.md), [Raycast](raycast.md)
- [Math Helpers](../core/advanced/math-helpers.md), [Precision and 64-bit](../core/advanced/precision-64bit.md)
- [Curve API](../core/advanced/curve-api.md)

## Verified against

- `core/pyx/_math_vec.pyx:1-470` (all methods; absence of `Vec3.rotate`, `Vec2.lerp`, `Vec4` ops, `Quat.lerp`)
- `core/math_helpers.py:1-381` (full function list, fallback header)
- `core/pyx/math_helpers.pyx:1-571` (Cython mirror)
- `core/maths/math3d.py:15-16`, `:108`, `:134-311` (flat helpers)
