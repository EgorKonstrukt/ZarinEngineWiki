# Spot Light
> Cone light: `SpotLight` (`core/components/lighting/lightp/light.py:304-381`) with lumen intensity, range and outer/inner cone angles.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Same lumens as point, but concentrated inside the cone: narrow cones look brighter than wide ones at equal lumens (inspector description, `light.py:317`). Gizmo draws the cone base circle + 8 ribs (`light.py:335-364`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `color` | color3 | `[1.0, 1.0, 1.0]` | Light color (`light.py:316`). |
| `intensity` | float lumens (0–200000) | `1000.0` | Flux inside the cone (`light.py:317,327`). |
| `range` | float (0–10000) | `10.0` | Cone length (`light.py:318,328`). |
| `spot_angle` | float (1–179) | `30.0` | Outer cone, degrees (`light.py:320,329`). |
| `spot_inner_angle` | float (0–179) | `20.0` | Inner falloff start, degrees (`light.py:321,330`). |
| `cast_shadows` | bool | `True` | Perspective shadow map; needs `SpotShadow` (`light.py:323,331`). |
## Usage in Editor
Aim with the transform; keep `spot_inner_angle < spot_angle` for a soft edge. Flashlight `100–1000` lm, street `1000–40000` lm.
## Usage in Code
```python
sp.spot_angle = 45.0
sp.spot_inner_angle = 30.0
sp.range = 25.0
```
## Related pages
- [Light](./light.md), [Point Light](./point-light.md), [Projector](./projector.md), [Shadows](./shadows.md)
## Verified against
- `core/components/lighting/lightp/light.py:304-381`
