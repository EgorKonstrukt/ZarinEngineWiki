# Projector
> Texture projector: `Projector` component (`core/components/lighting/projector.py`) throwing an image along the entity forward axis like a slide projector.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Perspective slide defined by `spot_angle`, `aspect_ratio`, `near_plane`/`far_plane`; `range` caps the throw distance. Gizmo draws the frustum + mid sprite quad (`projector.py:52-106`). Shadow pass hook: `render_projector_shadows` (`core/renderer/shadows/projector.py:28`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `texture_path` | resource path (png/jpg/jpeg) | `""` | Projected slide (`projector.py:22,40`). |
| `color` | color3 | `[1.0, 1.0, 1.0]` | Tint multiplier (`projector.py:23,41`). |
| `intensity` | float (0–1000) | `1.0` | Brightness (`projector.py:24,42`). |
| `range` | float (0–10000) | `10.0` | Throw distance (`projector.py:26,43`). |
| `spot_angle` | float (1–179) | `30.0` | Vertical FOV, deg (`projector.py:27,44`). |
| `aspect_ratio` | float (0.1–10) | `1.0` | Width/height ratio (`projector.py:28,45`). |
| `near_plane` | float (0.01–10) | `0.1` | Near clip (`projector.py:29,46`). |
| `far_plane` | float (1–10000) | `100.0` | Far clip (`projector.py:30,47`). |
| `flip_y` | bool | `True` | Flip image vertically (`projector.py:32,48`). |
| `flip_x` | bool | `False` | Flip image horizontally (`projector.py:33,49`). |
| `cast_shadows` | bool | `True` | Projector shadow cookie (`projector.py:34,50`). |
## Usage in Editor
Add Component → Projector, assign Texture, aim with the transform. Keep Aspect matched to the source image; enable Flip Y for camera-style slides.
## Usage in Code
```python
pj.texture_path = "assets/textures/logo.png"
pj.spot_angle = 40.0
pj.aspect_ratio = 16.0 / 9.0
pj.range = 20.0
```
## Related pages
- [Spot Light](./spot-light.md), [Shadows](./shadows.md), [Materials](./materials.md)
## Verified against
- `core/components/lighting/projector.py:18-50,108-140`
- `core/renderer/shadows/projector.py:28`
