# Entity

> `Entity` is a scene object: UUID `id`, `name`, parent/children hierarchy, `tags`, `layer`, `active` toggle, and a typed component store.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

`Entity(name="Entity", eid=None, prefab_guid=None)` generates a UUID when `eid` is omitted. Components live in `_components` (key → instance) plus `_type_map` (class → list). Single-instance components use the class name as key; `_allow_multiple` components get `ClassName.<8-hex>` keys. `add_component` calls `comp.on_awake()` immediately; `remove_*` call `on_destroy()`. Toggling `active` fires `on_enable`/`on_disable` on enabled components and adds/removes them from the scene's active update sets. `set_parent(parent, preserve_world=True)` keeps the world matrix by default.

## Properties

| name | type | default | description |
|---|---|---|---|
| id | str | UUID4 | Stable identity; scene dict key; serialized |
| name | str | `"Entity"` | Display name; `get_entity_by_name` matches it |
| active | bool | `True` | Inactive entities leave active update sets; `on_enable`/`on_disable` fire |
| parent | Entity\|None | `None` | Hierarchy link; `Scene.get_root_entities()` returns parentless entities |
| children | list[Entity] | `[]` | Direct children; `remove_entity` recurses into them |
| tags | set[str] | `set()` | Free-form tags; serialized as list |
| layer | int | `0` | Integer layer; serialized |
| transform | Component\|None | cached `Transform` | Fast path to the entity's `Transform` |
| prefab_guid | str\|None | `None` | Set → `is_prefab_instance` is `True` |
| prefab_source_id | str\|None | `None` | Source entity id inside the prefab |
| embed_resources | bool | `False` | Embed this entity's resources on save |
| locked | bool | `False` | Editor lock flag (e.g. `Shadow System`) |
| system | bool | `False` | System entity flag (e.g. `Shadow System`) |

## Usage in Editor

Hierarchy panel: create, rename, delete, duplicate, drag-to-reparent, toggle `active` checkbox. Locked/system entities (e.g. `Shadow System`) resist casual edits.

## Usage in Code

```python
from core.components.transform.tr import Transform

player = scene.create_entity("Player")
player.tags.add("player")
player.layer = 5
tr = player.add_component(Transform())

child = scene.create_entity("Gun")
child.set_parent(player)  # preserve_world=True by default

got = player.get_component(Transform)            # first match incl. subclasses
by_name = player.get_component_by_name("Transform")
all_tr = player.get_components(Transform)
player.remove_component(Transform)
scene.remove_entity(player.id)  # recursive over children
```

```python
clone = scene.duplicate_entity(player, "PlayerCopy")  # fresh UUIDs, Armature bone ids rebound
print(player.has_component(Transform), player.is_prefab_instance)
scene.get_root_entities()  # parentless entities
```

## Serialization

```json
{ "id": "<uuid>", "name": "Player", "active": true, "tags": ["player"],
  "layer": 5, "parent": null,
  "components": [{ "type": "Transform", "enabled": true, "_key": "Transform", "...": "..." }] }
```

Optional keys appear only when set: `prefab_guid`, `prefab_source_id`, `embed_resources: true`, `locked: true`, `system: true`.

## Related pages

- [ECS Overview](./ecs-overview.md)
- [Components and Lifecycle](./components-lifecycle.md)
- [Transform](./transform.md)
- [Registry and Serialization](./registry-serialization.md)

## Verified against

- `core/ecs/entity.py` (slots, defaults, `set_parent`, add/remove/get, `serialize`/`deserialize`)
- `core/ecs/scene.py` (`create_entity`, `add_entity`, `remove_entity`, `duplicate_entity`, `get_root_entities`)
- `core/engine/eng/engine.py` (`_ensure_shadow_system`: `system=True`, `locked=True`)
