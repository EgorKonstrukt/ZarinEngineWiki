# Math

`Vec2/Vec3/Vec4/Quat` (Cython `core/_math_vec`) plus `Mat4` (NumPy `float64`) and curve helpers; flat batch functions back hot loops.

## Properties / API

| Item | Source | Meaning |
|---|---|---|
| `Vec2/Vec3/Vec4/Quat` | `core/_math_vec` via `core/maths/math3d.py` | `FLOAT_TYPE` float64, `FLT_EPSILON` 1.19e-07 |
| `Mat4.identity/translation/scale/perspective/orthographic/look_at` | `core/maths/math3d.py` | Constructors; `__mul__/@`, `transposed`, `inverted`, `to_array/to_f32/to_list`, `get_translation`, `decompose()->(pos,Quat,scale)`, `batch_to_f32` |
| Flat helpers | `math3d.py` | `mat4_mul_flat`, `mat4_mul_vec_flat`, `dot3_flat`, `cross3_flat`, `normalize3_flat`, `rotate_around_axis_flat`, `look_at_flat`, `mat4_invert_flat`, `point_in_circle`, `coordinate_system_axes` |
| `convex_hull_simplices(points)` | `core/maths/convex_hull.py` | Cython `_convex_hull` or Python fallback |
| `Curve / QuaternionCurve` | `core/foundation/curve.py` | Float/quat keys used by `AnimationClip` and audio rolloff |

## Usage in Editor

1. Math types are Inspector primitives (`VEC3`, `FLOAT`, `CURVE` fields).
2. Curves edited in the curve editor (audio rolloff, animation keys).

## Usage in Code

```python
from core.maths.math3d import Vec3, Quat, Mat4

p = Vec3(1.0, 2.0, 3.0)
m = Mat4.translation(p) @ Mat4.scale(Vec3(2.0, 2.0, 2.0))
pos, rot, scl = m.decompose()
v = Mat4.perspective(60.0, 16/9, 0.1, 1000.0)
```

## Related

- Animation Clip, Constraints, NavAgent steering

## Verified against

- `core/maths/math3d.py`, `core/maths/convex_hull.py`, `core/foundation/curve.py`
