# ECS Overview

> ZarinEngine is an Entity-Component-System engine: `Entity` objects hold data, `Component` objects add behavior, `Scene` owns entities and dispatches `update` / `fixed_update`.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

Layer order (dependencies point down only): `main.py / player.py` → `editor/` → `plugins/` → `core/components/` → `core/renderer, physics, audio, network` → `core/ecs/` (`Entity`, `Component`, `Scene`, `ComponentRegistry`) → `core/foundation/` → `core/spatial, core/maths` (`ARCHITECTURE.md`). `core/` never imports `editor` or `plugins`; upward communication is `Engine.on` / `Engine.off` listeners plus `_emit_event` callbacks.

Runtime dispatch: `Engine.tick()` (play mode only) runs up to 5 fixed steps (`Scene.fixed_update(fd)`) then one `Scene.update(dt)`. `Scene` keeps `_active_update_components` / `_active_fixed_components` sets plus cached lists; `on_update` is partitioned into constraints → others → IK. Exceptions in callbacks are logged with entity and component names and never stop the frame.

## Properties

| name | type | default | description |
|---|---|---|---|
| Entity | class | `core/ecs/entity.py` | Identity + hierarchy + component storage |
| Component | class | `core/ecs/component.py` | Behavior/data unit with lifecycle hooks |
| Scene | class | `core/ecs/scene.py` | Entity owner, component index, update dispatch, Octree spatial index |
| ComponentRegistry | class | `core/ecs/registry.py` | Name → component class map, categories |
| public_import | statement | `from core.ecs import Component, Entity, Scene, ComponentRegistry` | Canonical import (`ARCHITECTURE.md`) |

## Usage in Editor

Entities are created in the Hierarchy panel; components are added via `Add Component` (registry categories: Transform, Rendering, Physics, Physics 2D, Lighting, Audio, Constraints, Network, Scripting). The Inspector edits fields; `enabled` toggles participation in updates.

## Usage in Code

```python
from core.ecs import Component, Entity, Scene, ComponentRegistry
from core.components.transform.tr import Transform

scene = Scene("Demo")
player = scene.create_entity("Player")
player.add_component(Transform())
scene.start()          # Scene.start() -> on_start() on all components
scene.update(0.016)    # on_update(dt) on active, enabled components
scene.fixed_update(0.02)
```

## Serialization

`Scene.serialize()` → `{"name": ..., "entities": {id: entity-dict}}`, optionally `embed_all`, `compress_resources`, `compress_level`. `Scene.deserialize(data, registry)` rebuilds entities and re-links parents. Thread-pool path above 64 entities.

## Related pages

- [Entity](./entity.md)
- [Components and Lifecycle](./components-lifecycle.md)
- [Registry and Serialization](./registry-serialization.md)
- [Transform](./transform.md)
- [Time and Fixed Update](./time.md)

## Verified against

- `ARCHITECTURE.md` (layers, modules, component rules)
- `core/ecs/__init__.py`, `core/ecs/entity.py`, `core/ecs/component.py`, `core/ecs/scene.py`, `core/ecs/registry.py`, `core/ecs/query.py`, `core/ecs/scene_serialize.py`
- `core/engine/eng/loop.py` (`tick`, `tick_begin`, `tick_fixed_step`, `tick_update`)
