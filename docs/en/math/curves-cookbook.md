# Curves Cookbook

Reference behaviour lives in [Curve API](../core/advanced/curve-api.md); this page shows verified usage patterns. `Curve`/`QuaternionCurve` are defined in `core/foundation/curve.py`.

## Facts used below

- `TangentMode`: `FREE` / `LINEAR` / `CONSTANT` / `SMOOTH` (default per key: `SMOOTH`).
- `evaluate(t)`: empty → `0.0`; single key → its value; outside range → clamped ends; `CONSTANT` left key → left value; `LINEAR` → lerp; else cubic Hermite (`1e-10` guards). Note: the scalar `evaluate` always clamps — `pre_wrap`/`post_wrap` (`"clamp"` defaults) are only forwarded on the Cython `evaluate_array` batch path.
- `evaluate_array(times)`: Cython `evaluate_curve_batch` when importable (mode map `FREE`/`SMOOTH`→0, `LINEAR`→1, `CONSTANT`→2), else per-sample `evaluate` fallback.
- `to_list()` → `[[time, value]]`; `from_list` rebuilds (entries with ≥ 2 items).
- `QuaternionCurve`: empty → `(0,0,0,1)`; segment slerp with `dot > 0.9995` lerp fallback and `1e-12` normalize guard; `add_key` sorts by time.

## Bobbing (looped by hand)

```python
import math
from core.foundation.curve import Curve

bob = Curve.from_list([[0.0, 0.0], [0.5, 1.0], [1.0, 0.0]])
t = 0.0
def on_update(dt):
    global t
    t = (t + dt * 0.5) % 1.0          # manual loop: wrap modes are clamp-only
    entity.transform.translate(__import__("core.maths.math3d", fromlist=["Vec3"]).Vec3(0, bob.evaluate(t) * 0.3 * dt * 60 * 0.016, 0))
```

Simpler, frame-exact variant with a sine-free curve sample:

```python
y = bob.evaluate(t) * 0.5            # 0..0.5 m offset
p = entity.transform.position
entity.transform.position = Vec3(p.x, base_y + y, p.z)
```

## Door easing (LINEAR vs SMOOTH)

```python
from core.foundation.curve import Curve, TangentMode

ease = Curve()
k0 = ease.add_key(0.0, 0.0)
k1 = ease.add_key(1.0, 90.0)
k0.tangent_mode = TangentMode.SMOOTH   # eased Hermite swing
# k0.tangent_mode = TangentMode.LINEAR # constant-speed swing instead
# k0.tangent_mode = TangentMode.CONSTANT # door stays shut until t >= 1.0

def on_update(dt):
    global t
    t = min(t + dt / 2.0, 1.0)         # 2-second open
    hinge.rotate(Vec3(0.0, ease.evaluate(t) - hinge.local_euler_angles.y, 0.0))
```

## Light animation (batch sampling)

```python
import numpy as np
from core.foundation.curve import Curve

flicker = Curve.from_list([[0.0, 1.0], [0.1, 0.6], [0.2, 1.0], [1.0, 0.9]])
samples = flicker.evaluate_array(np.linspace(0.0, 1.0, 60))  # Cython batch if available
print(flicker.to_list())  # [[0.0, 1.0], [0.1, 0.6], [0.2, 1.0], [1.0, 0.9]]
```

## Rotation keys

```python
from core.foundation.curve import QuaternionCurve
from core.maths.math3d import Quat

spin = QuaternionCurve()
spin.add_key(0.0, (0.0, 0.0, 0.0, 1.0))
spin.add_key(1.0, Quat.from_euler(0.0, 180.0, 0.0))  # Quat accepted
print(spin.evaluate(0.5))   # slerped midpoint tuple
print(spin.keys_dict())     # [{"time":..., "value":[...]}]
```

## Related

- [Curve API](../core/advanced/curve-api.md) (authoritative reference)
- [Math overview](overview.md), [Interpolation](interpolation.md)
- [Input and Math API](../scripting/input-math-api.md)

## Verified against

- `core/foundation/curve.py:14-27` (`TangentMode`, `CurveKey`), `:31-34` (defaults), `:36-70` (`add_key`, `_auto_smooth`), `:72-98` (`evaluate`), `:100-130` (`evaluate_array`, mode map), `:132-141` (`to_list`/`from_list`), `:239-269` (`QuaternionCurve`), `:191-221` (slerp/normalize guards)
