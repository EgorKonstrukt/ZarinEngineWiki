# Transform

> `Transform` (`core.components.transform.tr`) stores local position (`Vec3`), rotation (`Quat`), scale (`Vec3`) and computes the world matrix lazily from the parent chain.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

Defaults: position `(0,0,0)`, rotation identity `Quat(0,0,0,1)`, scale `(1,1,1)`. Any local setter marks the subtree dirty (`_mark_dirty` propagates to children, registers roots in `Scene._dirty_roots`, flags spatial + transform version). `world_matrix` recomputes on read when dirty: root entities write the local TRS matrix directly; children multiply `local @ parent_world`. The Inspector edits `local_position` (Position), `local_euler_angles` in degrees (Rotation), `local_scale` (Scale). `Entity.set_parent(..., preserve_world=True)` keeps the world matrix via the `world_matrix` setter. `position` reads the world translation; setting it converts to local space through the inverse parent matrix. `forward` is `-Z` of the world matrix (normalized), `right` is `+X`, `up` is `+Y`.

## Properties

| name | type | default | description |
|---|---|---|---|
| local_position | Vec3 | `(0,0,0)` | Accepts `Vec3`, `ndarray`, tuple/list |
| local_rotation | Quat | identity | Setter normalizes when given a `Quat` |
| local_scale | Vec3 | `(1,1,1)` | Accepts `Vec3`, `ndarray`, tuple/list |
| local_euler_angles | Vec3 | `(0,0,0)` deg | Euler view of the quaternion (`Quat.from_euler` on set) |
| position | Vec3 | world origin | World-space translation; setter converts to local |
| world_matrix | Mat4 | identity | Lazy; recomputed while `_dirty` |
| forward / right / up | Vec3 | axes | Normalized world axes (`-Z` / `+X` / `+Y`) |
| translate(delta, world_space=False) | method | — | Local add, or world-space via `position` |
| rotate(euler) | method | — | `euler` in degrees; post-multiplies local quaternion |
| look_at(target, up) | method | `up=None` (defaults to `Vec3.up()`) | Sets `local_rotation` via `Quat.look_rotation` |
| world set | setter | — | Deferred through `_world_target`, decomposed on flush |

## Usage in Editor

Inspector → Transform: Position / Rotation (degrees) / Scale numeric fields. Viewport gizmo drag writes the same locals. Reparent in Hierarchy preserves world placement (`preserve_world=True`).

## Usage in Code

```python
from core.components.transform.tr import Transform
from core.maths.math3d import Vec3

tr = entity.add_component(Transform())
tr.local_position = Vec3(0.0, 1.0, 0.0)
tr.local_euler_angles = Vec3(0.0, 45.0, 0.0)
tr.local_scale = Vec3(2.0, 2.0, 2.0)

tr.translate(Vec3(0.0, 0.0, 1.0))   # local move
tr.rotate(Vec3(0.0, 90.0, 0.0))     # degrees
print(tr.position)                  # world-space Vec3
print(tr.forward)                   # world -Z
```

```python
tr.translate(Vec3(1.0, 0.0, 0.0), world_space=True)  # via world position
tr.look_at(Vec3(0.0, 1.0, 0.0))  # local_rotation via Quat.look_rotation
```

## Serialization

```json
{ "type": "Transform", "enabled": true,
  "local_position": [0.0, 1.0, 0.0],
  "local_rotation": [0.0, 0.0, 0.0, 1.0],
  "local_scale": [1.0, 1.0, 1.0] }
```

Missing keys fall back to `[0,0,0]` / `[0,0,0,1]` / `[1,1,1]` in `Transform.deserialize`.

## Related pages

- [Entity](./entity.md)
- [ECS Overview](./ecs-overview.md)
- [Math](../math/overview.md)

## Verified against

- `core/components/transform/tr/__init__.py` (`Transform` class, registered)
- `core/components/transform/tr/component.py` (locals, euler, `_inspector_fields`, defaults)
- `core/components/transform/tr/matrix.py` (`world_matrix`, `position`, `forward/right/up`, `translate`, `rotate`, `look_at`)
- `core/components/transform/tr/hierarchy.py` (`_mark_dirty`, batch updates)
- `core/components/transform/tr/serialize.py` (keys + defaults)
- `core/ecs/entity.py` (`set_parent` world preservation)
