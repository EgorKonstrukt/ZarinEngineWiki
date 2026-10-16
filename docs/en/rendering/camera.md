# Camera
> Scene camera: `Camera` component (`core/components/rendering/cameras/camera.py`) with perspective/orthographic projection, depth ordering and native/custom render size.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
View matrix is `Mat4.look_at(position, position + forward, up)`; projection is `Mat4.perspective(fov, aspect, near, far)` or orthographic with `half_w = ortho_size * aspect` (`camera.py:56-69`). Gizmo draws the frustum from near/far corners (`camera.py:100-143`). Base clamps and render-size logic live in `CameraBase` (`camera_base.py`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `fov` | float (1.0–179.0) | `60.0` | Perspective field of view (`camera.py:36`, `camera_base.py:19`). |
| `near` | float (≥0.001) | `0.01` | Near clip (`camera.py:37`, `camera_base.py:20`). |
| `far` | float (≥0.1) | `1000.0` | Far clip (`camera.py:38`, `camera_base.py:21`). |
| `projection` | `CameraProjection` | `PERSPECTIVE` | `perspective` / `orthographic` (`camera.py:39,52`). |
| `ortho_size` | float (≥0.001) | `5.0` | Orthographic half-extent (`camera.py:40`, `camera_base.py:23`). |
| `depth` | int (−100…100) | `0` | Draw-order priority (`camera.py:42,54`). |
| `render_scale` | float (0.1–1.0) | `1.0` | Resolution multiplier (`camera.py:43`, `camera_base.py:24`). |
| `resolution_mode` | `CameraResolutionMode` | `native` | `native` follows viewport, `custom` uses w/h (`camera.py:44`). |
| `resolution_w` | int (1–16384) | `1920` | Custom width (`camera.py:45`). |
| `resolution_h` | int (1–16384) | `1080` | Custom height (`camera.py:46`). |
| `clear_color` | color4 (code only) | `[0.15, 0.15, 0.15, 1.0]` | Clear color, serialized but not in inspector (`camera.py:53,74`). |
## Usage in Editor
Add Component → Camera. Pick Projection; for ortho set Ortho Size. Use Depth to order multiple cameras. Use Resolution Mode `custom` + Viewport Width/Height for fixed-size captures, otherwise `render_scale` for cheap previews.
## Usage in Code
```python
cam = entity.get_component(Camera)
cam.fov = 55.0
cam.projection = CameraProjection.ORTHOGRAPHIC
cam.ortho_size = 8.0
view = cam.get_view_matrix()
proj = cam.get_projection_matrix(aspect=16.0 / 9.0)
```
## Related pages
- [Editor Camera](./editor-camera.md), [Pipeline](./pipeline.md), [Shadows](./shadows.md)
## Verified against
- `core/components/rendering/cameras/camera.py:32-98`
- `core/components/rendering/cameras/camera_base.py:12-111`
