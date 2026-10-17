# Prefab Scripting

Building, saving, and spawning prefabs from code, plus the five override types for instances.

## Prefab object

| Signature | Behavior |
|---|---|
| `Prefab(name="Prefab", guid=None, base_guid=None, menu_path=None)` | New prefab container; guid auto-`uuid4` unless given. `base_guid` set ⇒ variant. |
| `Prefab.create_via_code(name, build=None, menu_path=None, guid=None) -> Prefab` | Builds `roots_data` from a `build(prefab)` callable returning a list of entity dicts. |
| `prefab.capture(entities)` | Serializes entities into `roots_data`: stores `source_id`, drops live `parent` links, nests children recursively, strips `prefab_guid`. |
| `prefab.save(path)` | Writes `{guid, name, roots, base_guid?, menu_path?}` JSON; relativizes resource paths to the project root. Logs `Prefab saved: path`. |
| `Prefab.load(path) -> Prefab \| None` | Reads the file back (also accepts legacy `entity`/`entities` shapes). Logs on failure, returns `None`. |
| `prefab.instantiate(scene, registry, parent=None) -> list[Entity]` | Deep-copies roots, assigns **fresh UUIDs to every node**, deserializes, stamps `_prefab_guid`/`_prefab_source_id`, `scene.add_entity`, parents to `parent`. Returns root entities. |
| `prefab.instantiate_plain(scene, registry, parent=None) -> list[Entity]` | Same as `instantiate`, then `Prefab.unpack` on each root — plain copies with no prefab link. |
| `Prefab.unpack(entity_root)` | Clears `_prefab_guid`/`_prefab_source_id` on the root and **all descendants** (static, in place). |
| `Prefab.create_variant(base_prefab, name, guid=None) -> Prefab` | Variant sharing base data with its own guid + `base_guid`. |

Import: `from core.ecs.prefabp import Prefab` (also exports `PrefabOverrideType`, `PrefabLibrary`).

## The five override types

`PrefabOverrideType` values plus the functions that compute/apply them (defined in `overrides.py`, attached as `Prefab.*` statics):

| Type value | Meaning |
|---|---|
| `modified_property` | Name/active/tags/layer or one `Component.property` differs from the prefab source. Root `Transform` `local_position`/`local_rotation` are **excluded** (placement is per-instance by design). |
| `added_component` | Instance has a component the prefab lacks. |
| `removed_component` | Prefab has a component the instance lost. |
| `added_child` | Extra child entity under the instance. |
| `removed_child` | Prefab child missing from the instance. |

| Signature | Behavior |
|---|---|
| `Prefab.compute_overrides(entity) -> list[dict]` | Diff of one instance vs its resolved prefab source (variant chains resolved). `[]` for non-instances. |
| `Prefab.compute_all_overrides(entities) -> list[dict]` | Recursive over children; thread-pooled for >4 entities. |
| `Prefab.has_overrides(entities) -> bool` | `len(compute_all_overrides(...)) > 0`. |
| `Prefab.apply_overrides(entity_root)` | Writes the instance diff back into the `.zpep` file and invalidates the library cache. |
| `Prefab.revert_instance(root_entity, registry)` | Restores names, flags, components, and children from prefab source data (removes added children, re-creates missing ones with fresh ids). |

Each override dict carries `entity_id`, `entity_name`, `source_id`, `type`, plus `property`/`old_value`/`new_value` (or `component_type`/`child_source_id` variants).

## Enemy factory example (full, runnable)

Spawns prefab instances at runtime with per-instance placement (placement never counts as an override):

```python
from core.ecs import ComponentRegistry
from core.ecs.prefabp import Prefab
from core.maths.math3d import Vec3


class EnemyFactory:
    prefab_path: str = "assets/prefabs/enemy.zpep"
    spawn_gap: float = 3.0

    def on_start(self):
        self.scene = self._entity._scene
        self.prefab = Prefab.load(self.prefab_path)
        if self.prefab is None:
            Logger.error(f"EnemyFactory: cannot load {self.prefab_path}")
        self.spawned = 0

    def on_update(self, dt):
        if self.prefab is None:
            return
        if Input.GetKeyDown(KeyCode.E):
            roots = self.prefab.instantiate(
                self.scene, ComponentRegistry, parent=self._entity
            )
            for root in roots:
                root.name = f"Enemy_{self.spawned}"
                if root.transform is not None:
                    root.transform.local_position = Vec3(
                        float(self.spawned) * float(self.spawn_gap), 0.0, 0.0
                    )
            self.spawned += 1
            Logger.info(f"Spawned Enemy_{self.spawned - 1}")

    def report_overrides(self):
        for root in self.scene.get_root_entities():
            if root.is_prefab_instance:
                ovs = Prefab.compute_overrides(root)
                if ovs:
                    Logger.info(f"{root.name}: {len(ovs)} override(s)")
```

Capturing a prefab from a live hierarchy (editor tooling / bootstrap):

```python
from core.ecs.prefabp import Prefab

prefab = Prefab("Enemy")
prefab.capture([template_root_entity])   # template_root + descendants
prefab.save("assets/prefabs/enemy.zpep")

# spawn a linked instance later:
roots = Prefab.load("assets/prefabs/enemy.zpep").instantiate(scene, ComponentRegistry)

# ...or a plain, unlinked copy:
plain = Prefab.load("assets/prefabs/enemy.zpep").instantiate_plain(scene, ComponentRegistry)
```

## Related

- `entity-api.md` — `duplicate_entity` (scene-local copy) vs prefabs (asset-backed).
- `scene-api.md` — scenes that prefab instances live in.
- `pitfalls.md` — root transform placement is intentionally not an override.

## Verified against

- `core/ecs/prefabp/prefab.py:17-30` (5 `PrefabOverrideType` values + root-transform exclusions), `:45-59` (create/instantiate_plain), `:61-100` (capture/instantiate), `:122-129` (fresh-UUID remap), `:131-220` (save/load), `:354-366` (unpack/variant).
- `core/ecs/prefabp/overrides.py:13-54` (compute), `:209-244` (component diff + root skip), `:245-261` (has/apply), `:338-419` (revert + `Prefab.*` attachment).
- `core/ecs/prefabp/__init__.py` (exports `Prefab`, `PrefabOverrideType`, `PrefabLibrary`).
