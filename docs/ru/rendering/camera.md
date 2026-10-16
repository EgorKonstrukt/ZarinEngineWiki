# Камера
> Сценовая камера: компонент `Camera` (`core/components/rendering/cameras/camera.py`) с перспективной/ортографической проекцией, порядком depth и нативным/custom разрешением.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Матрица вида `Mat4.look_at(position, position + forward, up)`; проекция `Mat4.perspective(fov, aspect, near, far)` либо орто с `half_w = ortho_size * aspect` (`camera.py:56-69`). Гизмо рисует фрустум по углам near/far (`camera.py:100-143`). Кламы и логика размера — в `CameraBase` (`camera_base.py`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `fov` | float (1.0–179.0) | `60.0` | Угол обзора (`camera.py:36`, `camera_base.py:19`). |
| `near` | float (≥0.001) | `0.01` | Ближняя плоскость (`camera.py:37`, `camera_base.py:20`). |
| `far` | float (≥0.1) | `1000.0` | Дальняя плоскость (`camera.py:38`, `camera_base.py:21`). |
| `projection` | `CameraProjection` | `PERSPECTIVE` | `perspective` / `orthographic` (`camera.py:39,52`). |
| `ortho_size` | float (≥0.001) | `5.0` | Полусразмер орто (`camera.py:40`, `camera_base.py:23`). |
| `depth` | int (−100…100) | `0` | Приоритет порядка (`camera.py:42,54`). |
| `render_scale` | float (0.1–1.0) | `1.0` | Множитель разрешения (`camera.py:43`, `camera_base.py:24`). |
| `resolution_mode` | `CameraResolutionMode` | `native` | `native` — за вьюпортом, `custom` — w/h (`camera.py:44`). |
| `resolution_w` | int (1–16384) | `1920` | Custom ширина (`camera.py:45`). |
| `resolution_h` | int (1–16384) | `1080` | Custom высота (`camera.py:46`). |
| `clear_color` | color4 (только код) | `[0.15, 0.15, 0.15, 1.0]` | Цвет очистки, сериализуется, не в инспекторе (`camera.py:53,74`). |
## Usage in Editor
Add Component → Camera. Выбор Projection; для орто — Ortho Size. Depth упорядочивает несколько камер. Resolution Mode `custom` + Width/Height для фиксированных захватов, иначе `render_scale` для дешёвых превью.
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
