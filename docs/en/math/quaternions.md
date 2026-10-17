# Quaternions

`Quat` (`core/pyx/_math_vec.pyx:336-470`) is `(x, y, z, w)` `float64`, default identity `(0,0,0,1)`. Components are read-only properties. Heavy ops delegate to `core.math_helpers` (`quat_*`).

## Construction

| Signature | Units / notes |
|---|---|
| `Quat(x=0, y=0, z=0, w=1)` | — |
| `Quat.from_array(a)` | 4 floats |
| `Quat.identity()` | `(0,0,0,1)` |
| `Quat.from_euler(x, y, z)` | **degrees**, via `quat_from_euler` |
| `Quat.from_axis_angle(axis, angle_deg)` | axis auto-normalized; `angle_deg` in **degrees** (`math.radians(angle) * 0.5`) |
| `Quat.look_rotation(forward, up=None)` | `up` defaults to `Vec3.up()`; builds basis `r = up×f`, `u = f×r` |

There is no `Quat.from_euler_rad` classmethod — radian variants (`quat_from_euler_rad`) exist only as module functions in `math_helpers`.

## Operations

| Method | Behaviour |
|---|---|
| `__mul__(o)` | Hamilton product via `quat_mul` |
| `conjugate()` | `(-x,-y,-z,w)` |
| `normalized()` | guard `n > 1e-10`, else identity path |
| `rotate_vec3(v)` | rotates `Vec3`, returns `Vec3` |
| `to_euler()` | returns `Vec3` in **degrees** via `quat_to_euler` |
| `to_matrix4()` | normalizes first, returns `Mat4` |
| `slerp(o, t)` | via `quat_slerp` + renormalize (`1e-10` guard, else identity) |
| `to_list()` | `[x, y, z, w]` |

`slerp` details (`math_helpers`): sign-flip when `dot < 0`, lerp fallback when `dot > 0.9995` (then renormalize). No `lerp` method exists on `Quat`.

## Examples

```python
from core.maths.math3d import Quat, Vec3

q = Quat.from_euler(0.0, 90.0, 0.0)          # degrees!
print(q.rotate_vec3(Vec3(1, 0, 0)).to_list())  # ~[0.0, 0.0, -1.0]

spin = Quat.from_axis_angle(Vec3.up(), 45.0)   # 45 degrees about Y
print(spin.to_euler().to_list())               # [0.0, 45.0, 0.0] (degrees)

a = Quat.identity()
b = Quat.from_euler(0.0, 90.0, 0.0)
mid = a.slerp(b, 0.5)
print(mid.rotate_vec3(Vec3.forward()).to_list())

m = b.to_matrix4()  # Mat4 rotation matrix
```

## Related

- [Math overview](overview.md)
- [Vectors](vectors.md), [Matrices](matrices.md), [Transforms](transforms.md)
- [Math Helpers](../core/advanced/math-helpers.md)
- [Input and Math API](../scripting/input-math-api.md)

## Verified against

- `core/pyx/_math_vec.pyx:336-470` (`Quat`)
- `core/math_helpers.py:88-94` (`quat_mul`), `:97-119` (`quat_slerp`, `0.9995`), `:122-144` (`quat_from_euler`/`quat_to_euler`, degrees), `:147-165` (rad variants), `:168-184` (normalize, rotate)
