# Обзор
> Прямой OpenGL-рендерер: фасад `Renderer` в `core/renderer/renderer.py` рисует сцену во вьюпорт-FBO в две фазы — opaque, затем transparent.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Рендерер построен на moderngl. Контекст создаётся в `main.py`: `QSurfaceFormat`, OpenGL 4.6 Core, depth 24, swap interval из `rendering.vsync` (`0` выкл / `1` вкл). `Renderer` собирает миксины: config, programs, voxel, object-fx, framebuffers, water, uniforms, skinned, collector, geometry cache, outline, gizmo, icon, cubemap, post, scene (`core/renderer/renderer.py:56`). Режимы `RenderMode.SHADED / SHADED_WIREFRAME / FLAT` (`core/renderer/types.py`). Лимиты по умолчанию: `_max_lights = 8`, ambient `[0.26, 0.28, 0.34]`, обводка выделения `[0.8, 0.5, 0.1, 1.0]` толщиной `0.03` (`core/renderer/renderer.py:90-114`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `rendering.vsync` | bool (глобальный конфиг) | `True` | Swap interval 1 вкл / 0 выкл (`main.py:212`). |
| `rendering.max_lights` | int (глобальный конфиг) | `8` | Размер массива `u_lights[]` (`core/renderer/rconfig/config_state.py:20`). |
| `rendering.ambient` | vec3 (глобальный конфиг) | `[0.26, 0.28, 0.34]` | Резервный ambient (`core/renderer/renderer.py:112`). |
| `render_mode` | `RenderMode` | `SHADED` | Shaded / shaded+wireframe / flat (`core/renderer/types.py`). |
## Usage in Editor
Вьюпорт рисует активную сцену сценовой камерой; сетка, гизмо и обводка — только редакторные проходы. См. [Pipeline](./pipeline.md), [Camera](./camera.md), [Gizmos](./gizmos.md).
## Usage in Code
```python
from core.renderer.renderer import Renderer
renderer = Renderer(ctx)  # ctx: moderngl.Context
renderer.render_scene(scene, view_mat, proj_mat, cam_pos, w, h)
```
## Related pages
- [Pipeline](./pipeline.md), [Camera](./camera.md), [Materials](./materials.md), [Shaders](./shaders.md), [Gizmos](./gizmos.md)
## Verified against
- `main.py:206-213` (QSurfaceFormat 4.6 Core, depth 24, vsync)
- `core/renderer/renderer.py:56-120`
- `core/renderer/types.py`
- `core/renderer/rconfig/config_state.py:12-50`
