# Interpolation

Only helpers that exist in the code are documented here.

## What exists

| Helper | Location | Notes |
|---|---|---|
| `Vec3.lerp(o, t)` | `_math_vec.pyx:232-237` | `a + (b-a)*t`; no clamping of `t` |
| `Quat.slerp(o, t)` | `_math_vec.pyx:458-464` + `math_helpers.quat_slerp` | sign-flip for `dot < 0`; lerp fallback for `dot > 0.9995`; renormalize (`1e-10` guard) |
| `math_helpers.quat_slerp(...)` | `math_helpers.py:97-119` | scalar-tuple variant of the above |
| `Curve.evaluate(t)` | `curve.py:72-98` | Hermite / `LINEAR` lerp / `CONSTANT` step |
| `QuaternionCurve.evaluate(t)` | `curve.py:254-269` | slerp between neighbor keys |

## What does NOT exist

No `damp`, `smooth_damp`, `exp_damp`, `move_towards`, `lerp_angle` or `smoothstep` interpolation helpers were found in the math code. `damping` hits in the codebase are physics/particle parameters (`soft_body.damping`, PhysBone `damping_curve`, water `sim_damping`, editor-camera `damping` field) — not reusable math functions — and `smoothstep` appears only as a local shader helper in `hatching.py:259`. For frame-rate independent smoothing, scale the interpolation factor by `dt` yourself (see below).

## Frame-rate independence via `dt`

`lerp`/`slerp` take a raw `t` with no time concept. Drive `t` from accumulated time, or use exponential-style weighting per frame:

```python
from core.maths.math3d import Vec3, Quat

# Time-parameterized move (deterministic, dt-scaled):
pos = Vec3(0, 0, 0)
target = Vec3(10, 0, 0)
progress = 0.0
def on_update(dt):
    global progress, pos
    progress = min(progress + dt / 2.0, 1.0)  # 2 s travel
    pos = Vec3(0, 0, 0).lerp(target, progress)

# Per-frame exponential approach (frame-rate independent factor):
smooth = 5.0
current = Vec3(0, 0, 0)
def on_update_exp(dt):
    global current
    import math
    k = 1.0 - math.exp(-smooth * dt)   # your own weighting, not engine code
    current = current.lerp(target, k)

# Rotation:
a = Quat.identity()
b = Quat.from_euler(0.0, 90.0, 0.0)
mid = a.slerp(b, 0.5)
```

## Related

- [Math overview](overview.md)
- [Vectors](vectors.md), [Quaternions](quaternions.md), [Curves Cookbook](curves-cookbook.md)
- [Curve API](../core/advanced/curve-api.md)
- [Input and Math API](../scripting/input-math-api.md) (`dt` / `Input.deltaTime` pattern)

## Verified against

- `core/pyx/_math_vec.pyx:232-237` (`Vec3.lerp`), `:458-464` (`Quat.slerp`)
- `core/math_helpers.py:97-119` (`quat_slerp`)
- `core/foundation/curve.py:72-98`, `:254-269`
- Absence checked via codebase search for `damp*`, `move_towards`, `smoothstep` in math paths (only non-math hits listed above)
