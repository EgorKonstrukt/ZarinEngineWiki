# Entity API

Everything a gameplay script can do with entities and their components: creating, finding, reading, adding, removing, duplicating, parenting, tagging. For the callback side (when each method runs) see `lifecycle.md`; for field widgets see `inspector-fields.md`.

## Lookup: scene → entity

| Signature | Behavior |
|---|---|
| `Scene.create_entity(name="Entity", prefab_guid=None) -> Entity` | Creates a **bare** entity: no components, not even `Transform`. Marks scene dirty. |
| `Scene.add_entity(e)` | Registers an externally built `Entity` (wires indices, update sets, spatial state). |
| `Scene.remove_entity(eid)` | Removes the entity **and all its children recursively**. Calls `on_destroy` on components. |
| `Scene.get_entity(eid) -> Entity \| None` | By id string. |
| `Scene.get_entity_by_name(name) -> Entity \| None` | First entity with this name (names are not unique). |
| `Scene.get_all_entities() -> list[Entity]` | Cached flat list. |
| `Scene.get_root_entities() -> list[Entity]` | Entities with `parent is None` (cached, invalidated on parenting). |
| `Scene.get_entities_with_component(cls) -> list[Entity]` | Indexed query, subclass-aware. |
| `Scene.duplicate_entity(entity, new_name="") -> Entity` | Deep copy of the entity **and its whole subtree** with fresh UUIDs; Armature bone ids rebound. |
| `scene.mark_dirty()` / `scene.mark_clean()` / `scene.dirty` | Manual dirty flag (serialization and editor save indicators). |

Inside a script the scene is always at hand: `self._entity._scene` (assigned by the engine before `on_awake`).

## Lookup: entity → component

| Signature | Behavior |
|---|---|
| `entity.add_component(comp, key=None) -> Component` | Attaches; calls `comp.on_awake()`. Key defaults to `TypeName`, or `TypeName.<8 hex>` when the class allows multiples. |
| `entity.get_component(cls) -> Component \| None` | Exact type first, then `issubclass` fallback. Returns the **first** match. |
| `entity.get_components(cls) -> list` | All matches (exact or subclass). |
| `entity.get_component_by_name(name) -> Component \| None` | Resolution order: registered type name → exact component key → `"Name."` prefix (multi-instance) → `ComponentRegistry` subclass fallback. |
| `entity.has_component(cls) -> bool` | Exact or subclass presence. |
| `entity.get_all_components() -> list` | Every attached component. |
| `entity.get_component_in_children(cls)` | Depth-first search through children. |
| `entity.remove_component(cls)` | Removes first match, calls `on_destroy`. |
| `entity.remove_all_components(cls)` | Removes every match. |
| `entity.remove_component_by_key(key)` | Removes the exact keyed instance (use for multi-instance components). |

## Hierarchy, tags, flags

| Member | Behavior |
|---|---|
| `entity.set_parent(parent, preserve_world=True)` | Re-parents; with `True` (default) the world matrix is preserved via a `world_matrix` round-trip. Pass `None` to unparent. Depth caches are invalidated for the whole subtree. |
| `entity.parent` / `entity.children` | Read-only hierarchy links. |
| `entity.transform` | First component named `Transform` (cached), or `None` on bare entities. |
| `entity.active: bool` | Toggling deactivates the entity: update sets shrink, spatial entry drops, and `on_disable`/`on_enable` fire on every enabled component. |
| `comp.enabled: bool` | Per-component switch with the same update-set wiring. |
| `entity.tags: set[str]` | Free-form tag set (`entity.tags.add("enemy")`). Serialized as a list. |
| `entity.layer: int` | Integer layer, default `0`. |
| `entity.prefab_guid` / `entity.prefab_source_id` | Prefab linkage; `None` on plain entities. |
| `entity.is_prefab_instance: bool` | `True` when `prefab_guid` is set. |
| `entity.embed_resources` / `entity.locked` / `entity.system` | Serialization/editor flags. |

`duplicate_entity` details: every node in the copied subtree gets a new `uuid4`; children of copied nodes are re-linked inside the copy; a copied node whose original parent was **outside** the copied subtree is attached to that original parent; `new_name` renames the copy root. Armature `bone_entity_ids` are remapped through the old→new id map.

## Spawner example (full, runnable)

Uses only verified calls: bare `create_entity`, manual `Transform`, `set_parent`, `tags`, `get_root_entities`.

```python
from core.components.transform.tr import Transform
from core.maths.math3d import Vec3


class Spawner:
    spawn_name: str = "Pickup"
    count: int = 5
    spacing: float = 2.0

    def on_start(self):
        self.scene = self._entity._scene
        self.spawned = []
        for i in range(int(self.count)):
            e = self.scene.create_entity(f"{self.spawn_name}_{i}")
            t = Transform()
            t.local_position = Vec3(float(i) * float(self.spacing), 0.0, 0.0)
            e.add_component(t)
            e.tags.add("pickup")
            e.set_parent(self._entity, preserve_world=False)
            self.spawned.append(e.id)

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.SPACE):
            e = self.scene.create_entity(self.spawn_name)
            e.add_component(Transform())
            e.tags.add("pickup")

    def alive_roots(self):
        return [e.name for e in self.scene.get_root_entities()]
```

Inspector fields for this script: `spawn_name` (string), `count` (int), `spacing` (float) — plain annotated attributes.

Duplicating at runtime (fresh UUIDs guaranteed by the engine):

```python
class Cloner:
    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.C):
            scene = self._entity._scene
            copy = scene.duplicate_entity(self._entity, new_name="Clone")
            copy.set_parent(None, preserve_world=True)
```

Finding components defensively (multi-instance safe):

```python
class Reader:
    def on_start(self):
        e = self._entity
        tr = e.get_component_by_name("Transform")
        all_scripts = e.get_components(
            __import__("core.ecs", fromlist=["ComponentRegistry"]).ComponentRegistry.get("ScriptComponent")
        ) if e.has_component(
            __import__("core.ecs", fromlist=["ComponentRegistry"]).ComponentRegistry.get("ScriptComponent")
        ) else []
        Logger.info(f"transform={tr is not None} scripts={len(all_scripts)}")
```

Simpler idiom for the common case — cache in `on_start`, never per frame:

```python
class CacheDemo:
    def on_start(self):
        self.body = self._entity.get_component_by_name("Rigidbody")
        self.audio = self._entity.get_component_by_name("AudioSource")

    def on_update(self, dt):
        if self.body is not None and Input.GetKey(KeyCode.W):
            self.body.add_force(Vec3(0.0, 0.0, 10.0))
```

## Related

- `overview.md` — script discovery (`dir()` order) and multi-script entities.
- `lifecycle.md` — when `on_awake`/`on_start`/`on_destroy` run around these calls.
- `scene-api.md` — saving/loading the scenes these entities live in.
- `prefab-scripting.md` — spawning from prefabs instead of bare entities.
- `pitfalls.md` — `create_entity` has no `Transform`; `get_entity_by_name` is first-match.

## Verified against

- `core/ecs/entity.py:133-161` (`set_parent`), `:164-222` (`add_component`), `:223-333` (removes), `:334-384` (get/has), `:399-407` (children search), `:408-445` (serialize/deserialize), `:69-132` (active/tags/layer/prefab flags).
- `core/ecs/scene.py:229-322` (create/add/remove/duplicate/get/roots), `:217-218` (dirty).
- `core/ecs/scene_serialize.py:14-18` (Armature rebind), `:107-191` (duplicate with fresh UUIDs).
- `core/ecs/component.py` (`enabled` wiring, `_updates` auto-detect).
