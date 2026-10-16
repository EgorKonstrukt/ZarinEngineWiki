# Каталог гизмо

> Какие компоненты рисуют редакторные иконки, wireframe и линии: набор `Gizmo*`-компонентов, API отрисовки `gizmo_pass.py:19-68`, лайн-бэкенд, иконки света/частиц и маркер remote-коллаборатора.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Гизмо рисуются только во вьюпорте редактора, никогда в билдах. Поток данных: `gizmo()` / `gizmo_lines()` / `gizmo_meshes()` компонента → `GizmoPassMixin` (`core/renderer/gizmo_pass.py:19-68`) → лайн/инстанс/меш-бэкенды (`core/renderer/gizmor/lines.py:83-422`, `gizmor/icons.py:129-332`, `gizmor/renderer.py`).

## Draw API (`gizmo_pass.py:19-68`)

| method | backend |
|---|---|
| `render_gizmo_lines(lines, vp_mat, ...)` | `gizmo.render_lines` — жирные линии с множителем толщины. |
| `render_gizmo_arrays(starts, ends, colors, ...)` | `gizmo.render_raw_lines` — батч-массивы, ширина из `renderer._line_width` (`0.6667`) × 1.5. |
| `render_instanced_gizmo(mesh_type, ...)` | Инстансированные меши `cone/cylinder/cube/quad/circle`. |
| `render_gizmo_meshes / render_gizmo_mesh_np` | Произвольные списки мешей / NumPy-данные меша. |
| `render_instanced_gizmo_lines(shape_type, ...)` | Инстансированные линейные фигуры. |
| `render_wireframe_box(center, size, color, ...)` | Хелпер бокса из 12 рёбер. |

## Gizmo-компоненты (`core/components/gizmo/gizmo_comp/`, у всех `_gizmo_pass = "gizmo"`)

| module | components |
|---|---|
| `solids.py` | `GizmoCube`, `GizmoSphere`, `GizmoCylinder`, `GizmoCone`. |
| `lines.py` | `GizmoLine`, `GizmoRay`, `GizmoArrow`. |
| `planes.py` | `GizmoPlane`, `GizmoGrid`, `GizmoCircle`, `GizmoDisc`. |
| `bodies.py` | `GizmoCapsule`, `GizmoPyramid`, `GizmoFrustum`, `GizmoBounds`. |
| `curves.py` | `GizmoArc`, `GizmoTorus`, `GizmoCross`. |
| `paths.py` | `GizmoPoint`, `GizmoPoly`, `GizmoBezier`. |
| `extra.py` | `GizmoStar`, `GizmoIcon`, `GizmoLabel`. |

Ключевые поля: `GizmoLine.start/end` (`[-1,0,0]`/`[1,0,0]`), `GizmoCube.size` (`[1,1,1]`), `GizmoSphere.radius` (`0.5`), `GizmoIcon.icon` (билборд-текстура).

## Иконки и wireframe компонентов

| component | pass / label | drawing |
|---|---|---|
| `Light` base | `light` / `L` | Солнечный диск + 9 лучей (`light.py:101-130`). |
| `DirectionalLight` | `light` / `D` | Тот же солнечный глиф; кэширует `color`, `procedural_sky_lighting`. |
| `PointLight` | `light` / `P` | 3 кольца радиуса + 6 осевых шипов, радиус = `range`. |
| `SpotLight` | `light` / `S` | Конус дальности (база 32 сегмента + 8 спиц), угол = `spot_angle`. |
| `AreaLight` | `light` / `A` | Контур прямоугольника (+ кольцо диска для `DISK`, тусклый задний контур при double-sided). |
| `Projector` | `light` / `P` | Линии фрустума (`projector.py:56-106`). |
| `DirectionalShadow` | иконка `DSH` | Маркер одних настроек. |
| `ParticleSystem` | `particle` / `P` | Wireframe формы эмиттера (`partic/component.py:48-54`). |
| `ParticleForceField` | `force_field` / `FF` | Wireframe формы через `gizmo_lines()` (`particle_force_field.py:37-40,105`). |
| `VoxelizeEffect` | иконка `V` | Маркер объектного FX. |
| `VoxelRaymarchEffect` | иконка `VR` | Маркер пост-FX. |
| `RemoteCollaborator` | иконка `R` | Маркер пира: квадрат в плоскости вида + forward-тичка через `gizmo_lines()` (`remote_collaborator.py:24-37`). |
| `NavAgent` | `nav` / `N` | Маркер навигации. |
| `AudioSource` | `audio` / `A` | Маркер аудио. |

## Usage in Code

```python
from core.components.gizmo.gizmo_comp.lines import GizmoLine
gl = GizmoLine()
gl.start = [0, 0, 0]
gl.end = [0, 5, 0]
entity.add_component(gl)
```

## Related pages

- [Гизмо](../gizmos.md), [Свет](../light.md), [Система частиц](../particle-system.md), [Вьюпорт](../../editor/viewport.md)

## Verified against

- `core/renderer/gizmo_pass.py:19-70` (все шесть точек входа отрисовки)
- `core/renderer/gizmor/lines.py:83-422`, `core/renderer/gizmor/icons.py:129-332`
- `core/components/gizmo/gizmo_comp/lines.py:16-19,76-79,129-132`, `solids.py`, `planes.py`, `bodies.py`, `curves.py`, `paths.py`, `extra.py`
- `core/components/lighting/lightp/light.py:28-32,189-191,232-234,308-310` (буквы/проходы иконок)
- `core/components/lighting/lightp/shadows.py:83-86` (иконка/проход `AreaLight`)
- `core/components/rendering/particles/partic/component.py:48-54`, `particle_force_field.py:37-40`
- `core/components/network/remote_collaborator.py:13-37` (иконка `R`, маркер `gizmo_lines`)
- `core/renderer/renderer.py:135` (`_line_width = 0.6667`)
