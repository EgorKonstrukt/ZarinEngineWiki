# Coordinates

Only conventions confirmed by the code are stated. Anything else is marked unknown.

## Confirmed

| Convention | Evidence |
|---|---|
| Y-up | `Vec3.up()` = `(0,1,0)`; PhysX USDA export writes `upAxis = "Y"`; BSP importer documents `"Source (Z-up) -> ZarPy (Y-up)"` |
| Forward is −Z | `Vec3.forward()` = `(0,0,-1)`; `Transform.forward` = `−`row 2 of the world matrix |
| Right is +X | `Vec3.right()` = `(1,0,0)`; `Transform.right` = row 0 |
| 1 unit = 1 meter (physics export) | USD export writes `metersPerUnit = 1` |
| Inspector Rotation is degrees | Inspector `Rotation` edits `local_euler_angles`, which round-trips through degree-based `Quat.from_euler` / `Quat.to_euler` |
| Code rotation APIs take degrees… | `Quat.from_euler(x,y,z)`, `Quat.from_axis_angle(axis, angle_deg)`, `Transform.rotate(euler)`, `Mat4.perspective(fov_deg, ...)` |
| …except explicit `_rad` / `fov_rad` variants | `quat_from_euler_rad`, `quat_to_euler_rad`, `mat4_perspective(fov_rad, ...)` take radians |

## Not confirmed in code

- A global "world is meters" statement outside the physics USD export (`metersPerUnit = 1` in `solverp/scenes.py:63` is an export line, not a core constant).
- GUI left/right vs world mapping — no code found; not documented here.
- Any Z-up or left-handed mode at runtime: `coordinate_system_axes` / `is_right_handed_cs` helpers exist for conversion paths, but the runtime convention above is Y-up right-handed.

## Example

```python
from core.maths.math3d import Vec3, Quat

assert Vec3.up().to_list() == [0.0, 1.0, 0.0]
assert Vec3.forward().to_list() == [0.0, 0.0, -1.0]

q_deg = Quat.from_euler(0.0, 90.0, 0.0)   # degrees
print(q_deg.to_euler().to_list())         # degrees back

from core import math_helpers as mh
q_rad = mh.quat_from_euler_rad(0.0, 1.5707963, 0.0)  # radians variant
```

## Related

- [Math overview](overview.md)
- [Vectors](vectors.md), [Quaternions](quaternions.md), [Transforms](transforms.md)
- [Precision and 64-bit](../core/advanced/precision-64bit.md)

## Verified against

- `core/pyx/_math_vec.pyx:261-283` (`up`/`forward`/`right`)
- `core/components/transform/tr/matrix.py:281-318` (`forward`/`right`/`up`)
- `core/components/transform/tr/component.py:20` (Rotation field), `:108-116` (degree round-trip)
- `physics_solvers/physx_solver/solverp/scenes.py:63-64` (`metersPerUnit = 1`, `upAxis = "Y"`)
- `plugins/user/bsp_import/build.py:66` (Z-up → Y-up note)
- `core/math_helpers.py:122-165` (degree vs rad euler), `core/maths/math3d.py:42-44` (`fov_deg`)
