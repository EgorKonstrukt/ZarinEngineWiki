# Curve API

> `Curve` is a keyframed float function: `CurveKey(time, value, in/out_tangent, tangent_mode)` with `TangentMode` `FREE` / `LINEAR` / `CONSTANT` / `SMOOTH`. `evaluate(t)` clamps outside the key range and Hermite-interpolates inside; `to_list` stores `[[time, value]]`.

[Home](../../../index.md) | [Section index](./_section.md)

## Overview

`Curve` (`@dataclass`, fields `keys`, `pre_wrap="clamp"`, `post_wrap="clamp"`) and `CurveKey` (`time`, `value`, `in_tangent=0.0`, `out_tangent=0.0`, `tangent_mode=SMOOTH`) live in `core/foundation/curve.py`.

- `add_key(time, value)` appends and re-sorts by time, then `_auto_smooth()` recomputes tangents of `SMOOTH` keys only: endpoints use the neighbor slope, interior keys use the chord slope across both neighbors. Returns the new `CurveKey`. `remove_key(key)` and `find_key(time)` (epsilon `1e-6`) manage keys.
- `evaluate(time)`: empty curve → `0.0`; single key → its value; outside `[first, last]` → clamped end value. Inside a segment: `CONSTANT` mode on the left key returns the left value; `LINEAR` lerps; otherwise cubic Hermite with `m0 = out_tangent * dt`, `m1 = in_tangent * dt`.
- `evaluate_array(times)` uses the Cython `evaluate_curve_batch` path when importable (modes mapped `FREE/SMOOTH→0`, `LINEAR→1`, `CONSTANT→2`), else falls back to per-sample `evaluate`.
- Persistence: `to_list()` → `[[time, value]]`; `from_list(data)` rebuilds (entries with ≥ 2 items); `to_dict()` / `from_dict()` keep tangents, `tangent_mode` strings and wrap modes; `copy()` round-trips through the dict.
- `QuaternionCurve` is the rotation variant: keys hold 4-tuples (or `Quat`), `evaluate` slerps between neighbors, empty → `(0.0, 0.0, 0.0, 1.0)`; `keys_dict()` / `from_keys_dict()` persist as `[{"time", "value": [...]}]`.

## Properties

| name | type | default | description |
|---|---|---|---|
| `TangentMode` | Enum | `SMOOTH` | `free` / `linear` / `constant` / `smooth` |
| `CurveKey` | dataclass | — | `time`, `value`, `in/out_tangent`, `tangent_mode` |
| `Curve` | dataclass | `clamp`/`clamp` | `keys`, `pre_wrap`, `post_wrap` |
| `add_key(time, value)` | method | — | Sorted insert + SMOOTH re-smoothing |
| `evaluate(t)` | method | `0.0` empty | Clamped Hermite / LINEAR / CONSTANT |
| `evaluate_array(times)` | method | — | Numpy batch, Cython fast path with fallback |
| `to_list()` / `from_list()` | methods | — | Compact `[[t, v]]` form |
| `to_dict()` / `from_dict()` / `copy()` | methods | — | Full form with tangents and wraps |
| `QuaternionCurve` | class | identity empty | Slerp-interpolated rotation keys |

## Usage in Editor

`CURVE` inspector fields (e.g. PhysBone `pull_curve`, `spring_curve`, `gravity_curve`) open the curve editor panel, which edits the same `Curve` object (keys, tangents, per-key `TangentMode`).

## Usage in Code

```python
from core.foundation.curve import Curve, TangentMode, QuaternionCurve

curve = Curve()
k0 = curve.add_key(0.0, 0.0)
k1 = curve.add_key(1.0, 10.0)
k0.tangent_mode = TangentMode.LINEAR
print(curve.evaluate(0.5))   # 5.0
print(curve.to_list())       # [[0.0, 0.0], [1.0, 10.0]]

import numpy as np
print(curve.evaluate_array(np.array([0.0, 0.25, 0.5, 1.0])))

rot = QuaternionCurve()
rot.add_key(0.0, (0.0, 0.0, 0.0, 1.0))
print(rot.evaluate(0.0))
```

## Serialization

`to_list` is the compact scene form; `to_dict` is the full fidelity form (`keys` with `time/value/in_tangent/out_tangent/tangent_mode`, `pre_wrap`, `post_wrap`). `ScriptComponent` stores curves under its script-fields dict.

## Related pages

- [Foundation: Curve, Commands, Time Travel](../foundation.md)
- [Inspector Fields Catalog](./inspector-fields-catalog.md)
- [Math: Vec, Quat, Mat4, Curve](../../math/overview.md)
- [Curve, Gradient, Color Editors](../../editor/curve-gradient-editors.md)

## Verified against

- `core/foundation/curve.py` (`TangentMode`, `CurveKey`, `Curve`, `QuaternionCurve`, `_quat_slerp`, batch-mode mapping)
- `core/components/physics/physbone/params.py` (`FieldType.CURVE` usage)
- `core/components/scripting/scriptc/fields.py` (`Curve` annotation → `FieldType.CURVE`)
