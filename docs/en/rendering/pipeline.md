# Pipeline
> Forward pipeline in `SceneRendererMixin.render_scene`: collect snapshot → shadow maps → opaque phase → transparent phase → environment (sky/water/clouds) → editor overlays.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`render_scene(scene, view_mat, proj_mat, cam_pos, viewport_w, viewport_h, ...)` (`core/renderer/scenev/mixin.py:24`) applies `_render_scale` to get render size, collects a `_RenderSnapshot` (lights, `dir_light`, sky/cloud/water components, renderable entries), renders shadow maps via `ShadowRenderer`, then calls `_scenev_render_forward_phases(opaque, transparent, ...)` (`core/renderer/scenev/forward.py:19`). Transparent phase enables `SRC_ALPHA, ONE_MINUS_SRC_ALPHA` blending with `depth_mask = False`. Specialized passes: `WaterPassMixin`, `VoxelPassMixin`, `SkinnedPassMixin`, cubemap pass for dynamic reflections, `GizmoPassMixin`/`IconPassMixin`/`OutlinePassMixin` for the editor.
## Properties
| name | type | default | description |
|---|---|---|---|
| `_render_scale` | float | `1.0` | Global render-size multiplier (`core/renderer/scenev/mixin.py:27`). |
| `_shadow_resolution` | int | per-light-type | Shadow map size; see [Shadows](./shadows.md). |
| `_scene_fbo` | moderngl.Framebuffer | `None` until init | Scene color+depth target (`core/renderer/renderer.py:83-86`). |
## Usage in Editor
Viewport size comes from the Qt viewport; per-camera `render_scale` / custom resolution further scales it (see [Camera](./camera.md)). Stats overlay reads `_draw_calls`, `_opaque_draws`, `_trans_draws`, `_triangles_drawn`.
## Usage in Code
```python
renderer.render_scene(scene, cam.get_view_matrix(),
                      cam.get_projection_matrix(aspect),
                      cam_pos, 1280, 720)
```
## Related pages
- [Overview](./overview.md), [Camera](./camera.md), [Shadows](./shadows.md), [Water](./water.md), [Gizmos](./gizmos.md)
## Verified against
- `core/renderer/scencev/mixin.py` → `core/renderer/scenev/mixin.py:24-80`
- `core/renderer/scenev/forward.py:19-57`
- `core/renderer/renderer.py:56-120`
- `core/renderer/framebuffers.py`, `core/renderer/shadows/passes.py:183`
