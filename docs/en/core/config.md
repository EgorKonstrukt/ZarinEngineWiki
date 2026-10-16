# Configuration

> `Config` is JSON-backed settings with dot-notation access (`config.get("physics.gravity_y")`), defaults preservation, and change listeners. Global file: `~/.zarin/settings.json`; per-project: `<project>/ProjectSettings.json`.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

`Config(path, defaults)` loads JSON over a defaults dict (deep merge). `get` returns the caller default when any path segment is missing; `set` creates intermediate dicts and notifies `on_changed` listeners unless `notify=False`. `reset(key)` restores one key from defaults (or the whole file when `key=None`). Keys that need a restart are flagged with `mark_restart` (`editor.theme`, `editor.font_size`, `editor.ui_scale`, `editor.language`, `engine.python_jit`, `engine.python_optimize`, `engine.python_unbuffered`, `engine.python_no_bytecode`). `main.py` reads `engine.python_jit / python_optimize / python_unbuffered / python_no_bytecode` before startup and re-execs the interpreter when they differ.

## Properties

| name | type | default | description |
|---|---|---|---|
| get_global_config | function | singleton | Defaults include `editor`, `camera`, `profiler`, `rendering`, `gizmo`, `console`, `terminal`, `viewport`, `collab`, `undo`, `engine`, `hierarchy`, `inspector`, `project`, `plugins`, `audio`, `file_assoc`, `mesh_preview` |
| get_project_config | function | per path | Defaults: `project` (name, version, `default_scene=""`), `input`, `rendering`, `physics`, `audio` |
| engine.time_scale | float | `1.0` | Logic time multiplier |
| engine.fixed_update_dt | float | `0.02` | Fixed step (`Engine` clamps to `>= 0.001`) |
| rendering.vsync | bool | `True` | Swap interval in `main.py`/`player.py` |
| rendering.tick_rate | float | `120.0` | `GameWorker` update rate |
| rendering.fixed_tick_rate | float | `60.0` | `GameWorker` fixed rate |
| profiler.enabled | bool | `False` | Enables `engine.profiler` |
| physics.gravity_y | float | `-9.81` | Project default; `gravity_x/z = 0.0` |
| project.default_scene | str | `""` | Player fallback #3 |
| get / set / has / reset / to_dict / update / save | methods | — | Full `Config` API |
| on_changed(callback) | method | — | `callback(key, value)` on every notified `set` |

## Usage in Editor

Settings dialog edits the global config and saves to `~/.zarin/settings.json`. Restart-marked keys (`theme`, `ui_scale`, `language`, `python_*`) apply on next launch. Project settings (`ProjectSettings.json`) hold name, version, default scene, input bindings, physics solver parameters.

## Usage in Code

```python
from core.config.config import get_global_config, get_project_config

cfg = get_global_config()
dt = cfg.get("engine.fixed_update_dt", 0.02)
cfg.set("engine.time_scale", 0.5)
cfg.on_changed(lambda key, value: print(key, value))
cfg.save()

proj = get_project_config("/path/to/project")
print(proj.get("project.default_scene", ""))
```

```python
print(cfg.is_restart_key("editor.theme"))  # True: applies on next launch
cfg.reset("engine.time_scale")  # restore one key from defaults
```

## Serialization

The files are the format: pretty-printed JSON (`json.dump(..., indent=2)`). Example:

```json
{ "engine": { "time_scale": 1.0, "fixed_update_dt": 0.02 },
  "rendering": { "vsync": true, "tick_rate": 120.0 } }
```

## Related pages

- [Time and Fixed Update](./time.md)
- [Logging](./logging.md)
- [Profiler](./profiler.md)
- [First Scene](../getting-started/first-scene.md)

## Verified against

- `core/config/config.py` (`Config` API, `get_global_config` defaults, `get_project_config` defaults, paths, restart keys)
- `main.py` (`_apply_python_optimization`, `rendering.vsync`)
- `player.py` (`rendering.vsync`)
- `core/engine/eng/engine.py` (`initialize` reads `engine.time_scale`, `engine.fixed_update_dt`, `profiler.enabled`)
