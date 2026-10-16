# Editor Tour

> The Zarin editor is a PySide6 application (`EditorMainWindow`): 3D viewport, Hierarchy, Inspector, Project panel, Console, and Play controls.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

`main.py` builds the editor: OpenGL 4.6 Core `QSurfaceFormat`, application name `Zarin`, Fusion style, 8-step splash screen, `Engine.initialize()`, plugins from `plugins/` and `plugins/user/`, a `CollaborationManager`, then `EditorMainWindow` with the viewport assigned to `engine.viewport`. A second `main.py` launch forwards `.zpes` paths to the running instance via `IpcServer`.

## Properties

| name | type | default | description |
|---|---|---|---|
| window_class | class | `EditorMainWindow` | Main window (`editor/main_window`) |
| viewport | widget | ModernGL-based | Real-time 3D view; editor camera with orbit, pan, fly |
| hierarchy | panel | `editor/panels/hierarchy` | Entity tree; drag to reparent; right-click context menu |
| inspector | panel | `editor/inspector` | Auto-generated fields per component `InspectorField` metadata (`float`, `int`, `bool`, `string`, `color`, `enum`, `Vec2/Vec3`, resource path with picker, `curve`, list) |
| project | panel | `editor/panels/project` | Asset browser; `Project → Create → Python Script`; double-click `.py` opens the script editor |
| console | panel | `editor/panels/console` | Log listener view over `Logger` entries |
| terminal | panel | `editor/panels/terminal` | Built-in terminal |
| profiler_panel | panel | `editor/panels/profiler` | Frame timing overlay over `engine.profiler_data` |
| undo_history | panel | `editor/panels/undo_history_panel.py` | View over `CommandHistory` (`undo_text` / `redo_text`) |
| timetravel | panel | `editor/panels/timetravel` | View over `SnapshotRecorder` frames |
| scripts_panel | panel | `editor/panels/scripts_panel.py` | Shader-graph dock (`ScriptsPanel`, titled `Shaders`) |
| script_editor | panel | `editor/panels/script_editor_panel` | Script editor with `Check` / `Check Script` syntax check (`_check_current`) |
| splash_steps | int | `8` | `SplashScreen.set_total_steps(8)` in `main.py` |
| default_geometry | size | `1920x1080` | Centered on first start |

## Usage in Editor

1. Hierarchy: right-click → create entity; drag entities to reparent (`Entity.set_parent` with world preservation).
2. Inspector: `Add Component` lists `ComponentRegistry.all()` grouped by category; edit fields; per-component `enabled` toggle.
3. Project: create scripts, materials, scenes; scene files are `.zpes`.
4. Press Play: `Engine.start_play()`; press again to stop (`Engine.stop_play()` stops all audio via `AudioSourceManager.stop_all()`).

## Usage in Code

```python
from core.engine.eng import Engine

engine = Engine()
engine.initialize()
scene = engine.new_scene("MyScene")  # adds "Shadow System" entity, notifies plugins
engine.start_play()
# ... Engine.tick() is driven by GameWorker while in play mode ...
engine.stop_play()
```

## Serialization

Not applicable. Editor layout is not part of the scene format.

## Screenshots

![ZarinEngine editor main window](../../assets/screenshots/editor-main.png)

> Main window with viewport, Hierarchy, Inspector, Project panel, Console.
> File: `assets/screenshots/editor-main.png` (verified copy of engine
> `zarin_screenshot.png`).

## Related pages

- [Quick Start](./quick-start.md)
- [First Scene](./first-scene.md)
- [Entity](../core/entity.md)
- [Logging](../core/logging.md)
- [Profiler](../core/profiler.md)
- [Main Window](../editor/main-window.md)

## Verified against

- `main.py` (format, app name, splash steps, plugin dirs, `EditorMainWindow`, `engine.viewport`, geometry, `IpcServer`)
- `README.md` (Visual Editor section)
- `core/engine/eng/engine.py` (`new_scene`, `_add_default_scene_objects`, `start_play`, `stop_play`)
- `editor/panels/` (directory listing: hierarchy, project, console, terminal, profiler, timetravel, scripts_panel, script_editor_panel, undo_history_panel, collab, anim, terrain, prefab_editor_panel, plugin_manager_panel)
- `core/foundation/logger.py`, `core/foundation/profiler.py`, `core/foundation/cmd/history.py` (panel data sources)
