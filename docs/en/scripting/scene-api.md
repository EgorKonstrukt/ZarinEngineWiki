# Scene API

Creating, saving, and loading scenes from code. Entity-level operations live in `entity-api.md`; prefab spawning lives in `prefab-scripting.md`.

## Key fact first

`Scene.create_entity()` returns a **bare** entity with no components. If the entity must be visible or placed in the world, add a `Transform` yourself:

```python
from core.components.transform.tr import Transform

e = scene.create_entity("Prop")
e.add_component(Transform())
```

## Engine save/load (sync)

These live on the engine scenes mixin and block until done:

| Signature | Behavior |
|---|---|
| `engine.load_scene(path) -> Scene \| None` | Reads JSON, extracts embedded resources, resolves project-relative paths, deserializes, sets `scene.path` and `scene.name` (from file basename), marks clean, notifies plugins. Returns `None` on failure. |
| `engine.save_scene(path=None) -> None` | Serializes, embeds resources, relativizes paths, rotates backups (`path.bak`, `path.bak2`), writes atomically (`path.build` → rename), marks clean. Defaults to `scene.path`. |
| `engine.save_scene_object(scene, path=None) -> bool` | Same as `save_scene` but for an explicit scene object; returns success. |
| `engine.load_scene_from_data(data) -> Scene \| None` | Installs an already-deserialized dict as the active scene (network sync path). |

## Engine save/load (async)

Non-blocking variants for editor and loading screens; the callback runs on the GUI thread:

| Signature | Behavior |
|---|---|
| `engine.load_scene_async(path, data=None, on_done=None)` | Worker thread reads/parses/deserializes, then swaps on the GUI thread and calls `on_done(scene)`. Shows `scene:load` progress with throttled updates. |
| `engine.save_scene_async(path=None, on_done=None)` | Snapshots via `scene.serialize()`, persists on a worker thread, then marks clean and calls `on_done(ok)`. |

`on_done` is any callable: `on_done(scene)` for loads, `on_done(ok: bool)` for saves.

## Scene serialize/restore

| Signature | Behavior |
|---|---|
| `Scene.serialize() -> dict` | `{"name": ..., "entities": {eid: entity_dict}}` plus `embed_all` / `compress_resources` flags when set. Scenes over 64 entities serialize in a thread pool. |
| `Scene.deserialize(data, registry) -> Scene` | Rebuilds entities, re-links parents from stored parent ids, re-registers indices/update sets, clears dirty. |
| `scene.mark_dirty()` / `scene.mark_clean()` | Dirty flag + render version bump on dirty. |
| `scene.path` / `scene.name` / `scene.dirty` | File path, display name, unsaved-changes flag. |

## Level-loader example (full, runnable)

A menu script that loads levels asynchronously without freezing, using only verified signatures:

```python
class LevelLoader:
    level_path: str = "assets/scenes/level_02.zpes"

    def on_start(self):
        from core.engine.eng import Engine
        self.engine = Engine.instance()
        self.loading = False

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.F5) and not self.loading:
            self.loading = True
            Logger.info(f"Loading {self.level_path} ...")
            self.engine.load_scene_async(
                self.level_path,
                on_done=self._on_loaded,
            )

    def _on_loaded(self, scene):
        self.loading = False
        if scene is None:
            Logger.error(f"Failed to load {self.level_path}")
            return
        Logger.info(f"Loaded scene: {scene.name} ({len(scene.get_all_entities())} entities)")

    def on_update_save(self, dt):
        if Input.GetKeyDown(KeyCode.F6):
            self.engine.save_scene_async(on_done=lambda ok: Logger.info(f"Saved: {ok}"))
```

Sync save with explicit path (editor tooling / bootstrap code, not per-frame):

```python
from core.engine.eng import Engine

engine = Engine.instance()
engine.save_scene("assets/scenes/checkpoint.zpes")
scene = engine.load_scene("assets/scenes/checkpoint.zpes")
assert scene is not None and not scene.dirty
```

Round-tripping a scene snapshot in memory (verified `serialize`/`deserialize` pair):

```python
from core.ecs import Scene, ComponentRegistry

snapshot = scene.serialize()
clone = Scene.deserialize(snapshot, ComponentRegistry)
assert len(clone.get_all_entities()) == len(scene.get_all_entities())
```

## Related

- `entity-api.md` — what goes inside the scene.
- `prefab-scripting.md` — spawning prefab instances into the loaded scene.
- `hot-reload.md` — scene state is untouched by script reloads.
- `debugging.md` — load/save failures surface through `Logger.error`.

## Verified against

- `core/engine/eng/scenes.py:76-110` (`save_scene_async`), `:111-175` (`load_scene_async` + swap), `:332-365` (`load_scene`), `:366-391` (`load_scene_from_data`), `:392-416` (`save_scene`), `:417-443` (`save_scene_object`).
- `core/ecs/scene.py:229-239` (bare `create_entity`), `:361-414` (serialize/deserialize/caches), `:217-218` (dirty flag).
