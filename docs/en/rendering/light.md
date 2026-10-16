# Light
> Base light component `Light` (`core/components/lighting/lightp/light.py`): color, physical intensity and shadow toggle; concrete types set units and extra fields.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`LightType`: `directional / point / spot / area`; `LightAreaType`: `rect / disk` (`light.py:13-22`). Base `Light` is editor-hidden (`_editor_hidden = True`); add a concrete type instead. `Light.deserialize` dispatches on `light_type` to `DirectionalLight / PointLight / SpotLight / AreaLight` (`light.py:147-161`). Physical units: directional lux, point/spot lumens, area nits. Legacy scenes (`light_version < 2`) are rescaled by `LEGACY_DIRECTIONAL_MULT = 100000.0`, `LEGACY_POINT_MULT = 2000.0`, `LEGACY_AREA_MULT = 100.0` (`light.py:36-38`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `color` | color3 | `[1.0, 1.0, 1.0]` | Light color (`light.py:72,78`). |
| `intensity` | float (0–200000) | `100000.0` | Base default; concrete types override units (`light.py:73,79`). |
| `cast_shadows` | bool | `True` | Enable shadow-map contribution (`light.py:74,80`). |
## Usage in Editor
Add Directional/Point/Spot/Area Light (not base Light). White color + matching intensity presets from lux/lumen/nit references in code comments. Toggle `cast_shadows` off for fill lights.
## Usage in Code
```python
from core.components.lighting.lightp.light import DirectionalLight
sun = entity.get_component(DirectionalLight)
sun.color = [1.0, 0.95, 0.9]
sun.intensity = 100000.0
sun.cast_shadows = True
```
## Related pages
- [Directional Light](./directional-light.md), [Point Light](./point-light.md), [Spot Light](./spot-light.md), [Area Light](./area-light.md), [Shadows](./shadows.md), [Projector](./projector.md)
## Verified against
- `core/components/lighting/lightp/light.py:13-90,131-182`
