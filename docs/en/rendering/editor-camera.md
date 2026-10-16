# Editor Camera
> Viewport fly camera: `EditorCamera` component (`core/components/rendering/cameras/editor_camera.py`); right-mouse WASD/QE flight, middle-mouse pan, wheel dolly.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Rotation via right-mouse or Alt+Left drag (`rotate_speed`), pan via middle-mouse, dolly via wheel (`zoom_speed` with scroll acceleration). `on_update` flies with W/A/S/D + Q/E and Shift-style speed boost ramp (`FAST_MULT = 3.0`, `SPEED_BOOST_RAMP = 2.0`), accel `12.0`, damping `8.0` (`editor_camera.py:22-29,136-175`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `move_speed` | float (0.1–100.0) | `5.0` | Base fly/pan speed (`editor_camera.py:35,47`). |
| `rotate_speed` | float (0.01–5.0) | `0.3` | Mouse-look gain (`editor_camera.py:36,48`). |
| `zoom_speed` | float (0.1–50.0) | `4.0` | Wheel dolly gain (`editor_camera.py:37,49`). |
| `pan_speed` | float (0.001–0.1) | `0.01` | Middle-mouse pan gain (`editor_camera.py:38,50`). |
| `damping` | float (0.1–50.0) | `8.0` | Velocity smoothing (`editor_camera.py:39,51`). |
| `acceleration` | float (1.0–100.0) | `12.0` | Fly acceleration (`editor_camera.py:40,52`). |
## Usage in Editor
Hold right mouse + WASD to fly, Q/E for down/up; release to keep damped drift. Middle-drag pans; wheel dollies forward. Yaw/pitch persist via `serialize` (`yaw`, `pitch`).
## Usage in Code
```python
ec = entity.get_component(EditorCamera)
ec.move_speed = 8.0
ec.rotate_speed = 0.25
```
## Related pages
- [Camera](./camera.md), [Gizmos](./gizmos.md)
## Verified against
- `core/components/rendering/cameras/editor_camera.py:22-60,95-175`
