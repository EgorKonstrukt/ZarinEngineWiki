# Directional Light
> Sun-style infinite light: `DirectionalLight` (`core/components/lighting/lightp/light.py:185-225`) with lux intensity and optional procedural-sky driving.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Direction comes from the entity transform (`-forward` is used by sky/cloud shaders). Intensity is illuminance in lux: direct sun `100000`, daylight `10000–25000`, overcast `1000–10000`, sunrise `400`, office `500`, twilight `10`, full moon `0.2` (inspector description + `LUX_REFERENCE`, `light.py:40-48,198`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `color` | color3 | `[1.0, 1.0, 1.0]` | Light color (`light.py:197`). |
| `intensity` | float lux (0–200000) | `100000.0` | Illuminance; see scale above (`light.py:198,205`). |
| `procedural_sky_lighting` | bool | `False` | Drive/link procedural sky sun (`light.py:199,206`). |
| `cast_shadows` | bool | `True` | CSM contribution; needs active `DirectionalShadow` (`light.py:201,207`). |
## Usage in Editor
Rotate the entity to aim the sun. Pair with [Procedural Sky](./procedural-sky.md) via `procedural_sky_lighting` or `sun_light_entity_id`, and tune CSM in [Shadows](./shadows.md).
## Usage in Code
```python
sun.intensity = 15000.0  # full daylight
sun.procedural_sky_lighting = True
```
## Related pages
- [Light](./light.md), [Shadows](./shadows.md), [Procedural Sky](./procedural-sky.md), [Atmosphere](./atmosphere.md)
## Verified against
- `core/components/lighting/lightp/light.py:185-225`
