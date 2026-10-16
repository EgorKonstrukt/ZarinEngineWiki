# Registry and Serialization

> `ComponentRegistry` maps type names to component classes via the `@ComponentRegistry.register` decorator. Scenes serialize to `.zpes` JSON: `{"name", "entities": {id: entity-dict}}`.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

Registration is mandatory: an undecorated component class is invisible to `Add Component` menus and to deserialization (`ARCHITECTURE.md`: the architecture gate checks this). `ComponentRegistry.get(name)` resolves the class, honoring `_aliases` (`{"Sky": "ProceduralSky"}`). Categories are inferred from the module path (`core.components.<sub>` → display name via `_category_name_map`: transform→Transform, rendering→Rendering, physics→Physics, physics2d→Physics 2D, lighting→Lighting, audio→Audio, constraints→Constraints, network→Network, scripting→Scripting).

Scene serialization (`core/ecs/scene_serialize.py`): `{"name", "entities": {id: entity-dict}}` plus optional `embed_all`, `compress_resources`, `compress_level`. Deserialization rebuilds entities, then re-links `parent` ids and re-registers components in the scene index. Above 64 entities both directions use a thread pool. `duplicate_entity` / `paste_entities` assign fresh UUIDs and rebind `Armature.bone_entity_ids`.

## Properties

| name | type | default | description |
|---|---|---|---|
| ComponentRegistry.register | decorator | required | `ComponentRegistry._registry[name] = cls`; bumps subclass cache |
| ComponentRegistry.get | method | `get(name)` | Class or `None`; resolves `Sky` → `ProceduralSky` |
| ComponentRegistry.all | method | `all()` | Copy of the name → class map |
| get_categories | method | per component | e.g. `["Transform"]` for `Transform` |
| Scene.serialize | method | dict | `name`, `entities`, optional embed/compress flags |
| Scene.deserialize | classmethod | `deserialize(data, registry)` | Rebuild + parent re-link + index sync |
| PATH_FIELDS | frozenset | `core/config/constants.py` | `mesh_path, material_path, clip_path, script_path, texture_path, ply_path, shader_path, env_path, svg_path, font_path, video_path, graph_path, skybox_path` — resolved against `project_root` on load, relativized on save |

## Usage in Editor

`Add Component` groups come from `get_categories`. Unknown component types in a `.zpes` file are skipped silently on load (entity keeps the rest). Prefab instances carry `prefab_guid` / `prefab_source_id`; overrides serialize alongside the GUID.

## Usage in Code

```python
from core.ecs import Component, ComponentRegistry

@ComponentRegistry.register
class Health(Component):
    pass

assert ComponentRegistry.get("Health") is Health
print(ComponentRegistry.get_categories("Health"))  # category by module path

data = scene.serialize()
scene2 = Scene.deserialize(data, ComponentRegistry)
```

## Serialization

Full `.zpes` skeleton (entity keys: `id`, `name`, `active`, `tags`, `layer`, `parent`, `components`):

```json
{ "name": "MyScene",
  "entities": {
    "<uuid>": { "id": "<uuid>", "name": "E", "active": true, "tags": [],
                "layer": 0, "parent": null, "components": [] }
  } }
```

## Related pages

- [ECS Overview](./ecs-overview.md)
- [Entity](./entity.md)
- [Components and Lifecycle](./components-lifecycle.md)
- [Scenes](../scenes-prefabs/scenes.md)
- [Prefabs](../scenes-prefabs/prefabs.md)

## Verified against

- `core/ecs/registry.py` (`register`, `get`, `all`, `_aliases`, `_category_name_map`, `_infer_category`)
- `core/ecs/scene_serialize.py` (`_serialize_inner`, `_deserialize_inner`, `_duplicate_entity_inner`, `_rebind_armatures`)
- `core/ecs/entity.py` (`serialize`/`deserialize`, unknown-type skip)
- `core/config/constants.py` (`PATH_FIELDS`)
- `core/engine/eng/engine.py` (`resolve_scene_paths`, `relativize_scene_paths`)
- `ARCHITECTURE.md` (register decorator mandatory)
