# Overview
> Forward OpenGL renderer: `Renderer` facade in `core/renderer/renderer.py` draws the scene into the viewport FBO in opaque then transparent phases.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
ZarinEngine uses a forward renderer built on moderngl. Entry context is created in `main.py`: `QSurfaceFormat`, OpenGL 4.6 Core profile, depth buffer 24, swap interval from `rendering.vsync` (`0` off / `1` on). `Renderer` composes mixins: config, programs, voxel, object-fx, framebuffers, water, uniforms, skinned, collector, geometry cache, outline, gizmo, icon, cubemap, post, scene (`core/renderer/renderer.py:56`). Render modes are `RenderMode.SHADED / SHADED_WIREFRAME / FLAT` (`core/renderer/types.py`). Default caps: `_max_lights = 8`, ambient `[0.26, 0.28, 0.34]`, selection outline `[0.8, 0.5, 0.1, 1.0]` thickness `0.03` (`core/renderer/renderer.py:90-114`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `rendering.vsync` | bool (global config) | `True` | Swap interval 1 on / 0 off (`main.py:212`). |
| `rendering.max_lights` | int (global config) | `8` | Size of `u_lights[]` uniform array (`core/renderer/rconfig/config_state.py:20`). |
| `rendering.ambient` | vec3 (global config) | `[0.26, 0.28, 0.34]` | Fallback ambient (`core/renderer/renderer.py:112`). |
| `render_mode` | `RenderMode` | `SHADED` | Shaded / shaded+wireframe / flat (`core/renderer/types.py`). |
## Usage in Editor
Viewport renders the active scene with the scene camera; grid, gizmos and outline are editor-only passes. See [Pipeline](./pipeline.md), [Camera](./camera.md), [Gizmos](./gizmos.md).
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
