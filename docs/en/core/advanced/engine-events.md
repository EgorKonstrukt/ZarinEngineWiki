# Engine Events

> The engine bus is three methods on `Engine` (`EngineEventsMixin`): `on(event, callback)`, `off(event, callback)`, `_emit_event(event, data)`. Callback exceptions are caught and logged; with ≥ 10 listeners the emit fans out over the shared thread pool.

[Home](../../../index.md) | [Section index](./_section.md)

## Overview

`core/engine/eng/events.py` implements the bus: `on` appends to `_event_listeners[event]`; `off` removes; `_emit_event` calls listeners synchronously when fewer than 10 (each wrapped in try/except → `Event callback error '<event>'`), otherwise submits them to the shared pool (`core.ecs.pool.general`) and waits via `as_completed`. `_defer_gui` posts through `_gui_poster` when set, else runs inline.

Engine-emitted events (all verified at emit sites):

| event | payload | emitted from |
|---|---|---|
| `history_changed` | `None` | `CommandHistory.execute` / `seek` always; `undo` / `redo` with listeners |
| `scene_loaded` | `Scene` | new/open/load scene paths, Play restore, time-travel restore |
| `scene_saved` | `Scene` / scene | save paths (including the daemon save worker) |
| `entity_created` | entity | project-panel entity creation |
| `play_start` / `play_stop` | `None` | `Engine.start_play` / `stop_play` |

Plugin hooks are a separate channel (`PluginBase` no-op methods, fanned out by `PluginManager.notify_*`): `on_viewport_ready(viewport)` (called directly from the `Engine.viewport` setter), `on_scene_loaded(scene)` / `on_scene_unloaded(scene)`, `on_project_opened()`, `on_play_start()` / `on_play_stop()` (called alongside the `play_start`/`play_stop` events), `on_main_window_ready(window)` (via `Engine.set_main_window`). `step(dt)` / `pre_step(dt)` are per-frame plugin ticks, not events. Example plugin hooks used in-tree: `example_plugin.py` (`on_scene_loaded`, `on_play_start/stop`), `physics_drag_plugin.py` (+ `on_viewport_ready`), `qt_quick_plugin` (+ `on_scene_unloaded`).

## Properties

| name | signature | description |
|---|---|---|
| `on(event, cb)` | method | Subscribe |
| `off(event, cb)` | method | Unsubscribe (missing → no-op) |
| `_emit_event(event, data)` | method | Sync < 10 listeners, pooled otherwise |
| `history_changed` | event | Undo-stack change signal |
| `scene_loaded` / `scene_saved` | events | Scene lifecycle signal |
| `play_start` / `play_stop` | events | Play-mode signal |
| `PluginBase.on_*` | hooks | Parallel plugin lifecycle channel |

## Usage in Editor

The hierarchy panel refreshes on `engine.on("history_changed", ...)`; time-travel playback and scene tabs emit `scene_loaded` after swaps so every panel rebinds. Editor code subscribes with `on` and never calls plugin `notify_*` directly (that is the `PluginManager`'s job).

## Usage in Code

```python
def refresh(_):
    print("history changed")

engine.on("history_changed", refresh)
engine.on("scene_loaded", lambda scene: print("loaded:", scene))
engine.on("play_start", lambda _: print("play"))
# ...
engine.off("history_changed", refresh)
```

```python
from core.foundation.plugins.base import PluginBase

class MyPlugin(PluginBase):
    NAME = "MyPlugin"

    def on_scene_loaded(self, scene):
        print("scene:", scene)

    def on_play_start(self):
        print("play started")

    def on_main_window_ready(self, main_window):
        print("ui ready")
```

## Serialization

Events carry live objects (`Scene`, entity) and are never serialized.

## Related pages

- [Commands and Undo](./commands-undo.md)
- [Time Travel](./time-travel.md)
- [PluginBase](../../plugins/pluginbase.md)
- [Plugin Manager](../../plugins/plugin-manager.md)
- [Example Plugin Walkthrough](../../plugins/example-plugin.md)

## Verified against

- `core/engine/eng/events.py` (`on`, `off`, `_emit_event`, pool fan-out threshold, `_defer_gui`)
- `core/engine/eng/engine.py` (`start_play`/`stop_play` events + `notify_play_start/stop`, `set_main_window`, `viewport` setter)
- `core/engine/eng/scenes.py` (`scene_loaded` / `scene_saved` emits)
- `core/foundation/cmd/history.py` (`history_changed` emit policy)
- `core/foundation/plugins/base.py` (all `PluginBase.on_*` hooks)
- `core/foundation/plugins/lifecycle.py` (`notify_*` fan-out)
- `editor/panels/hierarchy/panel.py` (`history_changed` subscription)
- `editor/panels/project/openers.py` (`entity_created` emit)
