# Scenes

`Scene` is the entity store; `EngineScenesMixin` loads/saves it. Prefab links are GUID-based; overrides live on instances.

## Properties / API

| Item | Source | Meaning |
|---|---|---|
| `Scene.get_all_entities() / get_entity(id)` | `core/ecs/scene.py` | Entity access |
| `Engine.load_scene(path) / load_scene_from_data(data)` | `core/engine/eng/scenes.py` | Deserialize scene JSON into entities |
| `Engine.save_scene(path) / save_scene_object(scene, path)` | `core/engine/eng/scenes.py` | Serialize entities to JSON file |
| `load_scene_async / save_scene_async` | `core/engine/eng/scenes.py` | Background variants with `on_done` callback |
| Entity fields | `core/ecs/entity.py` | `id`, `name`, `active`, `parent`, `children`, `components`, `prefab_guid`, `prefab_source_id` |

## Usage in Editor

1. Build the hierarchy; each entity keeps a stable `id`.
2. Save via scene save; file stores entity dicts with components and prefab GUIDs.
3. Loading recreates entities via `Entity.deserialize` + `ComponentRegistry`, then re-links parents and prefab sources.

## Usage in Code

```python
eng = Engine.instance()
scene = eng.load_scene("assets/scenes/level1.json")
eng.save_scene("assets/scenes/level1.json")
```

## Related

- Prefabs and Library, ECS Entity, Components lifecycle

## Verified against

- `core/engine/eng/scenes.py`, `core/ecs/scene.py`, `core/ecs/entity.py`
