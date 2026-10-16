# Gizmo Catalog

> Which components draw editor icons, wireframes, and lines: the `Gizmo*` component set, the `gizmo_pass.py:19-68` draw API, the line backend, light/particle icons, and the remote-collaborator marker.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Gizmos render only in the editor viewport, never in builds. Data flows: component `gizmo()` / `gizmo_lines()` / `gizmo_meshes()` → `GizmoPassMixin` (`core/renderer/gizmo_pass.py:19-68`) → line/instanced/mesh backends (`core/renderer/gizmor/lines.py:83-422`, `gizmor/icons.py:129-332`, `gizmor/renderer.py`).

## Draw API (`gizmo_pass.py:19-68`)

| method | backend |
|---|---|
| `render_gizmo_lines(lines, vp_mat, ...)` | `gizmo.render_lines` — fat lines with thickness multiplier. |
| `render_gizmo_arrays(starts, ends, colors, ...)` | `gizmo.render_raw_lines` — batched arrays, width from `renderer._line_width` (`0.6667`) × 1.5. |
| `render_instanced_gizmo(mesh_type, ...)` | `cone/cylinder/cube/quad/circle` instanced meshes. |
| `render_gizmo_meshes / render_gizmo_mesh_np` | Arbitrary mesh lists / NumPy mesh data. |
| `render_instanced_gizmo_lines(shape_type, ...)` | Instanced line shapes. |
| `render_wireframe_box(center, size, color, ...)` | 12-edge box helper. |

## Gizmo components (`core/components/gizmo/gizmo_comp/`, all `_gizmo_pass = "gizmo"`)

| module | components |
|---|---|
| `solids.py` | `GizmoCube`, `GizmoSphere`, `GizmoCylinder`, `GizmoCone`. |
| `lines.py` | `GizmoLine`, `GizmoRay`, `GizmoArrow`. |
| `planes.py` | `GizmoPlane`, `GizmoGrid`, `GizmoCircle`, `GizmoDisc`. |
| `bodies.py` | `GizmoCapsule`, `GizmoPyramid`, `GizmoFrustum`, `GizmoBounds`. |
| `curves.py` | `GizmoArc`, `GizmoTorus`, `GizmoCross`. |
| `paths.py` | `GizmoPoint`, `GizmoPoly`, `GizmoBezier`. |
| `extra.py` | `GizmoStar`, `GizmoIcon`, `GizmoLabel`. |

Key fields: `GizmoLine.start/end` (`[-1,0,0]`/`[1,0,0]`), `GizmoCube.size` (`[1,1,1]`), `GizmoSphere.radius` (`0.5`), `GizmoIcon.icon` (billboard texture).

## Component icons and wireframes

| component | pass / label | drawing |
|---|---|---|
| `Light` base | `light` / `L` | Sun disc + 9 ray lines (`light.py:101-130`). |
| `DirectionalLight` | `light` / `D` | Same sun glyph; caches `color`, `procedural_sky_lighting`. |
| `PointLight` | `light` / `P` | 3 range rings + 6 axis spikes, radius = `range`. |
| `SpotLight` | `light` / `S` | Range cone (32-seg base + 8 spokes), angle = `spot_angle`. |
| `AreaLight` | `light` / `A` | Rect outline (+ disk ring for `DISK`, dim back rect when double-sided). |
| `Projector` | `light` / `P` | Frustum lines (`projector.py:56-106`). |
| `DirectionalShadow` | icon `DSH` | Settings-only marker. |
| `ParticleSystem` | `particle` / `P` | Emitter shape wireframe (`partic/component.py:48-54`). |
| `ParticleForceField` | `force_field` / `FF` | Shape wireframe via `gizmo_lines()` (`particle_force_field.py:37-40,105`). |
| `VoxelizeEffect` | icon `V` | Object-FX marker. |
| `VoxelRaymarchEffect` | icon `VR` | Post-FX marker. |
| `RemoteCollaborator` | icon `R` | Peer marker: view-plane square + forward tick drawn by `gizmo_lines()` (`remote_collaborator.py:24-37`). |
| `NavAgent` | `nav` / `N` | Navigation marker. |
| `AudioSource` | `audio` / `A` | Audio marker. |

## Usage in Code

```python
from core.components.gizmo.gizmo_comp.lines import GizmoLine
gl = GizmoLine()
gl.start = [0, 0, 0]
gl.end = [0, 5, 0]
entity.add_component(gl)
```

## Related pages

- [Gizmos](../gizmos.md), [Light](../light.md), [Particle System](../particle-system.md), [Viewport](../../editor/viewport.md)

## Verified against

- `core/renderer/gizmo_pass.py:19-70` (all six draw entry points)
- `core/renderer/gizmor/lines.py:83-422`, `core/renderer/gizmor/icons.py:129-332`
- `core/components/gizmo/gizmo_comp/lines.py:16-19,76-79,129-132`, `solids.py`, `planes.py`, `bodies.py`, `curves.py`, `paths.py`, `extra.py`
- `core/components/lighting/lightp/light.py:28-32,189-191,232-234,308-310` (icon letters/passes)
- `core/components/lighting/lightp/shadows.py:83-86` (`AreaLight` icon/pass)
- `core/components/rendering/particles/partic/component.py:48-54`, `particle_force_field.py:37-40`
- `core/components/network/remote_collaborator.py:13-37` (icon `R`, `gizmo_lines` marker)
- `core/renderer/renderer.py:135` (`_line_width = 0.6667`)
