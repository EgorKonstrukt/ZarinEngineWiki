# Point Light
> Omnidirectional bulb: `PointLight` (`core/components/lighting/lightp/light.py:228-301`) with lumen intensity and finite range.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Total flux in lumens: candle `12.57`, desk lamp `300`, room ceiling `800`, 100W bulb `1600`, street `15000`, stadium `100000` (`LUMEN_REFERENCE`, `light.py:49-57,241`). Gizmo draws three range circles plus axis ticks (`light.py:254-286`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `color` | color3 | `[1.0, 1.0, 1.0]` | Light color (`light.py:240`). |
| `intensity` | float lumens (0–200000) | `1000.0` | Total flux (`light.py:241,248`). |
| `range` | float (0–10000) | `10.0` | Cutoff distance (`light.py:242,249`). |
| `cast_shadows` | bool | `True` | Cube-map shadows; needs active `PointShadow` (`light.py:244,250`). |
## Usage in Editor
Place at bulb height, set Range to the room radius, intensity from the lumen table. Disable shadows for cheap fills.
## Usage in Code
```python
pl.intensity = 800.0  # room ceiling
pl.range = 12.0
```
## Related pages
- [Light](./light.md), [Spot Light](./spot-light.md), [Shadows](./shadows.md)
## Verified against
- `core/components/lighting/lightp/light.py:228-301`
