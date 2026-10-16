# First Scene

> Create a scene in code or in the editor: entities with `Transform`, a light, a camera, then `save_scene_async` to `.zpes`.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

`Engine.new_scene(name)` creates an empty `Scene` plus a locked system entity `Shadow System` (holds `DirectionalShadow`, `PointShadow`, `SpotShadow`, `AreaShadow`). Every entity you add gets an `id` (UUID), `name`, `active=True`, `tags=set()`, `layer=0`. Save with `save_scene_async(path)`; load with `load_scene_async(path)` (both in `EngineScenesMixin`; synchronous `save_scene(path)` / `load_scene(path)` variants exist for scripts). Saves rotate two backups (`.bak`, `.bak2`).

## Properties

| name | type | default | description |
|---|---|---|---|
| new_scene | method | `Engine.new_scene(name="NewScene")` | Creates scene, default objects, emits `scene_loaded` |
| create_entity | method | `Scene.create_entity(name="Entity")` | Entity bound to the scene, marked dirty |
| save | method | `Engine.save_scene_async(path)` | Async save; emits `scene_saved`; keeps `.bak` backups |
| load | method | `Engine.load_scene_async(path)` | Async load with progress task `scene:load` |
| scene_file | format | `.zpes` JSON | `{"name": ..., "entities": {id: entity-dict}}` |

## Usage in Editor

1. `File → New Scene` (or start from `new_scene`).
2. Add entities in the Hierarchy; move them with the viewport gizmo (writes `Transform.local_position` / `local_euler_angles` / `local_scale`).
3. Add a camera and a light via `Add Component → Rendering / Lighting`.
4. Save the scene as `.zpes`. Run it headless with `python player.py MyScene.zpes`.

## Usage in Code

```python
from core.engine.eng import Engine
from core.components.transform.tr import Transform
from core.maths.math3d import Vec3

engine = Engine()
engine.initialize()
scene = engine.new_scene("FirstScene")

player = scene.create_entity("Player")
tr = player.add_component(Transform())
tr.local_position = Vec3(0.0, 1.0, 0.0)

engine.save_scene_async("scenes/FirstScene.zpes")
```

## Serialization

Minimal `.zpes` fragment:

```json
{ "name": "FirstScene",
  "entities": {
    "c0ffee00-0000-4000-8000-000000000001": {
      "id": "c0ffee00-0000-4000-8000-000000000001",
      "name": "Player", "active": true, "tags": [], "layer": 0, "parent": null,
      "components": [
        { "type": "Transform", "enabled": true, "_key": "Transform",
          "local_position": [0.0, 1.0, 0.0],
          "local_rotation": [0.0, 0.0, 0.0, 1.0],
          "local_scale": [1.0, 1.0, 1.0] }
      ]
    }
  } }
```

## Related pages

- [Quick Start](./quick-start.md)
- [Entity](../core/entity.md)
- [Transform](../core/transform.md)
- [Registry and Serialization](../core/registry-serialization.md)
- [Scenes](../scenes-prefabs/scenes.md)

## Verified against

- `core/engine/eng/engine.py` (`new_scene`, `_ensure_shadow_system`, `_add_default_scene_objects`)
- `core/engine/eng/scenes.py` (`save_scene_async`, `load_scene_async`, `_rotate_scene_backups`, `_SCENE_BACKUP_KEEP = 2`)
- `core/ecs/entity.py` (`Entity.__init__` defaults, `serialize`)
- `core/components/transform/tr/serialize.py` (Transform keys)
