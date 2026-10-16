# Конвейер
> Прямой конвейер в `SceneRendererMixin.render_scene`: сбор снапшота → карты теней → фаза opaque → фаза transparent → окружение (небо/вода/облака) → редакторные оверлеи.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`render_scene(scene, view_mat, proj_mat, cam_pos, viewport_w, viewport_h, ...)` (`core/renderer/scenev/mixin.py:24`) применяет `_render_scale`, собирает `_RenderSnapshot` (свет, `dir_light`, компоненты неба/облаков/воды, рисуемые записи), рисует тени через `ShadowRenderer`, затем вызывает `_scenev_render_forward_phases(opaque, transparent, ...)` (`core/renderer/scenev/forward.py:19`). Transparent-фаза включает блендинг `SRC_ALPHA, ONE_MINUS_SRC_ALPHA` с `depth_mask = False`. Отдельные проходы: `WaterPassMixin`, `VoxelPassMixin`, `SkinnedPassMixin`, cubemap для динамических отражений, `GizmoPassMixin`/`IconPassMixin`/`OutlinePassMixin` для редактора.
## Properties
| name | type | default | description |
|---|---|---|---|
| `_render_scale` | float | `1.0` | Глобальный множитель размера (`core/renderer/scenev/mixin.py:27`). |
| `_shadow_resolution` | int | по типу света | Размер карты теней; см. [Shadows](./shadows.md). |
| `_scene_fbo` | moderngl.Framebuffer | `None` до init | Цель color+depth (`core/renderer/renderer.py:83-86`). |
## Usage in Editor
Размер вьюпорта приходит из Qt-вьюпорта; `render_scale` / custom resolution камеры дополнительно масштабируют (см. [Camera](./camera.md)). Оверлей статистики читает `_draw_calls`, `_opaque_draws`, `_trans_draws`, `_triangles_drawn`.
## Usage in Code
```python
renderer.render_scene(scene, cam.get_view_matrix(),
                      cam.get_projection_matrix(aspect),
                      cam_pos, 1280, 720)
```
## Related pages
- [Overview](./overview.md), [Camera](./camera.md), [Shadows](./shadows.md), [Water](./water.md), [Gizmos](./gizmos.md)
## Verified against
- `core/renderer/scenev/mixin.py:24-80`
- `core/renderer/scenev/forward.py:19-57`
- `core/renderer/renderer.py:56-120`
- `core/renderer/framebuffers.py`, `core/renderer/shadows/passes.py:183`
