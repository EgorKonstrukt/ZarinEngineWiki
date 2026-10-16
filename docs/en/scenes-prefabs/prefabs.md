# Prefabs and Library

`Prefab` captures entity subtrees to JSON (`.zpep`), re-instantiates them with fresh GUIDs, and tracks per-instance overrides.

## Properties / API

| Item | Source | Meaning |
|---|---|---|
| `Prefab(name, guid, base_guid, menu_path)` | `core/ecs/prefabp/prefab.py` | `guid` identity, `base_guid` marks a variant, `roots_data` payload |
| `capture(entities)` | `prefab.py` | `Entity.serialize` + `source_id`, strips `prefab_guid`/`parent`, recurses children |
| `save(path)` | `prefab.py` | JSON `{guid, name, roots[, base_guid, menu_path]}`; relatives resolved, `_key` stripped |
| `instantiate(scene, registry, parent)` | `prefab.py` | Deep-copy roots, `_remap_ids` (uuid4), `Entity.deserialize`, set `_prefab_guid/_prefab_source_id` |
| `instantiate_plain(scene, registry, parent)` | `prefab.py` | Instantiate then `Prefab.unpack` (detach from prefab) |
| `PrefabOverrideType` | `prefab.py` | `modified_property / added_component / removed_component / added_child / removed_child` |
| `compute_overrides / compute_all_overrides / has_overrides / apply_overrides` | `core/ecs/prefabp/overrides.py` | Diff instance vs asset, write back to `.zpep` |
| `PrefabLibrary` | `core/ecs/prefabp/library.py` | Asset cache/index |
| `register_system_prefab / create_system_prefab / ensure_builtin_prefabs` | `core/prefabs/registry.py`, `builtins.py` | Built-ins from `system_prefabs.yaml` |
| File filter | `core/components/network/network_spawn.py`, `scriptc/fields.py` | `Prefab (*.zpep)` |

## Usage in Editor

1. Select root entity, create prefab asset (`.zpep` JSON with `guid`, `name`, `roots`).
2. Drag instances into scenes; edits on an instance are recorded as overrides, not asset edits.
3. Apply overrides to write instance diff back to the `.zpep` file.

## Usage in Code

```python
from core.ecs.prefabp.prefab import Prefab

pref = Prefab(name="Enemy")
pref.capture([enemy_entity])
pref.save("assets/prefabs/enemy.zpep")

spawned = pref.instantiate(scene, registry, parent=None)
plain = pref.instantiate_plain(scene, registry)
```

## Related

- Scenes, ECS Entity, Network spawn (`*.zpep` filter)

## Verified against

- `core/ecs/prefabp/prefab.py`, `core/ecs/prefabp/overrides.py`, `core/ecs/prefabp/library.py`
- `core/prefabs/registry.py`, `core/prefabs/builtins.py`
