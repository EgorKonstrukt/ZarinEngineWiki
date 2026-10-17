# Vectors

`Vec2`, `Vec3`, `Vec4` are Cython types compiled from `core/pyx/_math_vec.pyx` and re-exported through `core/maths/math3d.py`. All components are `float64`.

## Constructors

| Type | Signature | Defaults | Notes |
|---|---|---|---|
| `Vec2` | `Vec2(x=0.0, y=0.0)` | `0.0, 0.0` | `x`/`y` have setters |
| `Vec3` | `Vec3(x=0.0, y=0.0, z=0.0)` | `0.0, 0.0, 0.0` | `x`/`y`/`z` have setters; `__getitem__` 0–2 (`IndexError` otherwise); iterable |
| `Vec4` | `Vec4(x=0.0, y=0.0, z=0.0, w=1.0)` | note `w=1.0` | `x`/`y`/`z`/`w` are **read-only** (no setters) |
| `from_array` | `Vec2.from_array(a)` / `Vec3.from_array(a)` / `Vec4.from_array(a)` | — | `float(a[i])` per component |

Static shortcuts exist only on `Vec2`/`Vec3`: `zero()`, `one()`. `Vec3` additionally has `up()` → `(0,1,0)`, `forward()` → `(0,0,-1)`, `right()` → `(1,0,0)`. `Vec4` has no shortcuts.

## Operators (component-wise, scalar second operand)

| Method | `Vec2` | `Vec3` | `Vec4` |
|---|---|---|---|
| `+`, `-` (vector) | yes | yes | **no** |
| `*`, `__rmul__`, `/` (scalar) | yes | yes | **no** |
| unary `-` | yes | yes | **no** |
| `dot(o)` | yes | yes | **no** |
| `cross(o)` | **no** | yes | **no** |
| `length()` | yes | yes | **no** |
| `length_sq()` | **no** | yes | **no** |
| `distance_to(o)` / `distance_sq_to(o)` | **no** | yes | **no** |
| `normalized()` | yes | yes | **no** |
| `lerp(o, t)` | **no** | yes | **no** |
| `to_list()` | yes | yes | yes |
| `to_array()` (`float64`) | **no** | yes | **no** |

Guards: `normalized()` returns the zero vector when length `<= 1e-10`. `__eq__` uses tolerance `1e-8` per component. `__repr__` prints 4 decimals (`Vec3(1.0000, ...)`).

Not in the code: `Vec3.rotate()` does not exist — rotation goes through `Quat.rotate_vec3` or `math3d.rotate_around_axis_flat`. `Vec2` has no `lerp`, `cross`, `distance_to`; `Vec4` has no arithmetic at all.

## Examples

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
print(Vec3().normalized().to_list())  # [0.0, 0.0, 0.0] (guard 1e-10)

p = Vec4(1.0, 2.0, 3.0)  # w defaults to 1.0
print(p.to_list())       # [1.0, 2.0, 3.0, 1.0]
```

## Related

- [Math overview](overview.md)
- [Matrices](matrices.md), [Quaternions](quaternions.md)
- [Curve API](../core/advanced/curve-api.md)
- [Math Helpers](../core/advanced/math-helpers.md), [Precision and 64-bit](../core/advanced/precision-64bit.md)
- [Input and Math API](../scripting/input-math-api.md)

## Verified against

- `core/pyx/_math_vec.pyx:17-109` (`Vec2`), `:111-287` (`Vec3`), `:289-333` (`Vec4`)
- `core/maths/math3d.py:11` (re-export), `:15` (`FLOAT_TYPE`)
