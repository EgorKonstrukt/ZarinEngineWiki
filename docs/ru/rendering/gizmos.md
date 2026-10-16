# Гизмо
> Отладочная отрисовка редактора: `GizmoRenderer` (`core/renderer/gizmor/renderer.py`, линии `gizmor/lines.py`), питает `GizmoPassMixin` (`core/renderer/gizmo_pass.py`) и компоненты `Gizmo*` (`core/components/gizmo/gizmo_comp/`).
[Home](../../index.md) · [Section index](./_section.md)
## Overview
API прохода: `render_gizmo_lines / render_gizmo_arrays / render_instanced_gizmo / render_gizmo_meshes / render_wireframe_box` (`gizmo_pass.py:19-68`). Бэкенд линий: `render_lines / render_raw_lines / render_wireframe_box` (`gizmor/lines.py:83-422`); инстансинг иконок/мешей (`gizmor/icons.py:129-332`). Компоненты: `GizmoCube/Sphere/Cylinder/Cone` (`solids.py`), `GizmoLine/Ray/Arrow` (`lines.py`), `GizmoPlane/Grid/Circle/Disc` (`planes.py`), `GizmoCapsule/Pyramid/Frustum/Bounds` (`bodies.py`), `GizmoArc/Torus/Cross` (`curves.py`), `GizmoPoint/Poly/Bezier` (`paths.py`), `GizmoStar/Icon/Label` (`extra.py`). Иконки света/камеры/частиц — через `_gizmo_pass = "light"/"camera"/"particle"` и `_gizmo_cache_attrs`.
## Properties
| name | type | default | description |
|---|---|---|---|
| `GizmoLine.start` / `end` | vec3 | `[-1,0,0]` / `[1,0,0]` | Концы отрезка (`lines.py:21-30`). |
| `GizmoCube.size` | vec3 | `[1,1,1]` | Размер бокса (`solids.py:21-30`). |
| `GizmoSphere.radius` | float | `0.5` | Радиус (`solids.py:82-90`). |
| `GizmoIcon.icon` | texture | дефолт движка | Билборд-иконка (`extra.py:78-90`). |
| `gizmo.line_width` | float (глобальный конфиг) | дефолт рендерера | Толщина fat-линий (`rconfig/config_state.py:51-56`). |
## Usage in Editor
Компоненты Gizmo* — для разметки уровней (триггеры, спавны, точки путей). Видны только во вьюпорте редактора, в билды не попадают.
## Usage in Code
```python
from core.components.gizmo.gizmo_comp.lines import GizmoLine
gl = GizmoLine()
gl.start = [0, 0, 0]
gl.end = [0, 5, 0]
entity.add_component(gl)
```
## Related pages
- [Camera](./camera.md), [Light](./light.md), [Particle Force Field](./particle-force-field.md)
## Verified against
- `core/renderer/gizmo_pass.py:19-68`
- `core/renderer/gizmor/lines.py:83-422`, `core/renderer/gizmor/icons.py`
- `core/components/gizmo/gizmo_comp/lines.py`, `solids.py`, `planes.py`, `bodies.py`, `curves.py`, `paths.py`, `extra.py`
- `core/gizmo/pipeline.py`, `core/gizmo/core/`
