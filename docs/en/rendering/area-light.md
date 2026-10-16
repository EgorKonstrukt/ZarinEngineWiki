# Area Light
> Panel light: `AreaLight` (`core/components/lighting/lightp/shadows.py:79-185`) with nit brightness, rect/disk shape and multi-sample shadows.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Surface brightness in nits (cd/m²): monitor `200`, office panel `500`, overcast sky `2000`, softbox `10000`. Same nits on a bigger panel lights more (`shadows.py:92`). Gizmo draws the rect frame, optional disk ring and double-sided back frame (`shadows.py:118-161`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `color` | color3 | `[1.0, 1.0, 1.0]` | Light color (`shadows.py:91`). |
| `intensity` | float nits (0–200000) | `100.0` | Surface brightness (`shadows.py:92,106`). |
| `range` | float (0–10000) | `10.0` | Cutoff distance (`shadows.py:93,107`). |
| `area_type` | `LightAreaType` | `RECT` | `rect` / `disk` (`shadows.py:95,108`). |
| `area_width` | float (0.01–100) | `1.0` | Panel width, m (`shadows.py:96,109`). |
| `area_height` | float (0.01–100) | `1.0` | Panel height, m (`shadows.py:97,110`). |
| `area_double_sided` | bool | `False` | Emit from both faces (`shadows.py:98,111`). |
| `area_samples` | int (1–64) | `6` | Shadow samples (`shadows.py:99,112`). |
| `area_shadow_bias` | float (0–0.1) | `0.005` | Shadow bias (`shadows.py:100,113`). |
| `cast_shadows` | bool | `True` | Needs active `AreaShadow` (`shadows.py:102,114`). |
## Usage in Editor
Scale via Area Width/Height, not the transform. Use Disk for round softboxes; enable Double Sided for paper lanterns. Raise Samples to soften, Bias to fix acne.
## Usage in Code
```python
al.area_type = LightAreaType.DISK
al.area_width = 2.0
al.area_samples = 12
```
## Related pages
- [Light](./light.md), [Shadows](./shadows.md)
## Verified against
- `core/components/lighting/lightp/shadows.py:79-185`
- `core/components/lighting/lightp/light.py:20-22` (`LightAreaType`)
