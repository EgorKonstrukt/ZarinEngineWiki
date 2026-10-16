# Main Window

> `EditorMainWindow` (`editor/main_window/widgetp/frame.py`) — the `QMainWindow` shell of the editor: docks, toolbar, menus, status bar, session autosave.

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `EditorMainWindow` | `(engine: Engine)` | Mixes `_ToolbarHostMixin`, `_StatusMixin`; re-exported by `editor/main_window.py` |
| `_setup_engine_events` | `(self)` | Wires engine signals into the window |
| `_schedule_session_autosave` | `(delay_ms: int = 5000)` | Debounced session persist |
| `_flush_session_autosave` | `(self)` | Writes the pending session snapshot |
| `closeEvent` | `(event: QCloseEvent)` | Flush path on window close |
| `toggle_play_stop` | `(mw)` in `main_window/handlersp/play.py` | Play/Stop button + `Shift+F10` action |
| `toggle_pause` | `(mw)` in `main_window/handlersp/play.py` | Pause button |
| `launch_external_player` | `(mw)` in `main_window/handlersp/play.py` | " Player" toolbar button |
| `on_open_prefab_editor` | `(mw, path: str)` in `handlersp/prefab.py` | Opens prefab tab, starts autosave |
| `on_project_file_double_clicked` | `(mw, path: str)` in `handlersp/prefab.py` | Double-click routing for project files |
| `on_entity_dropped` | `(mw, path_or_type: str, world_pos, entity_under_cursor=None)` in `handlersp/drop_assets.py` | Viewport drop routing (material/image/audio/model) |
| `_StatusMixin._update_status` | `(self)` | Status bar refresh incl. hardware monitor (`_HwMonitorWorker`: cpu/ram/gpu/vram) |

## Usage in Editor

Menu shortcuts verified in `main_window/menup/builder.py`:

| Action | Shortcut |
|---|---|
| New Scene | `Ctrl+N` |
| Open Scene... | `Ctrl+O` |
| Save Scene | `Ctrl+S` |
| Save Scene As... | `Ctrl+Shift+S` |
| Save All Scenes | `Ctrl+Alt+S` |
| Create Empty (GameObject menu) | `Ctrl+Shift+N` |
| Play/Stop | `Shift+F10` |
| Exit | `Alt+F4` |
| Search Everywhere (command palette) | `Ctrl+F` |

Additional verified shortcuts in the same file (same source, omitted from the short table — all from `menup/builder.py`):

| Action | Shortcut |
|---|---|
| Launch in Player | `F10` |
| Go to Tab... | `Ctrl+P` |
| Next / Previous Tab | `Ctrl+Tab` / `Ctrl+Shift+Tab` |
| Plugin Manager | `Ctrl+Shift+P` |
| Python Packages... | `Ctrl+Shift+K` |
| Mesh Editor | `Ctrl+Shift+M` |
| Terrain Editor | `Ctrl+Shift+T` |
| GUI Editor | `Ctrl+Shift+G` |
| Build Settings... | `Ctrl+Shift+U` |
| Build Project... | `Ctrl+Shift+B` |

Toolbar: Play/Stop (`toggle_play_stop`), Pause, ` Player` button (tooltip "Launch in standalone Player"), search button (tooltip "Search Everywhere (Ctrl+F)"), fly/orbit nav-mode combo (tooltip documents WASD + RMB look, MMB pan, wheel dolly, Shift+RMB cursor place, Ctrl+MMB dolly).

## Usage in Code

```python
from editor.main_window.handlersp.play import toggle_play_stop, toggle_pause, launch_external_player

toggle_play_stop(mw)      # same as Play button / Shift+F10
toggle_pause(mw)          # same as Pause button
launch_external_player(mw)  # standalone player for the current scene
```

## Screenshots

![ZarinEngine editor main window](../../assets/screenshots/editor-main.png)

> `EditorMainWindow` with docks, toolbar, viewport, status bar.
> File: `assets/screenshots/editor-main.png` (verified copy of engine
> `zarin_screenshot.png`).

## Related

- Viewport (scene view hosted in the main window), Hierarchy, Inspector, Project Panel
- Build Dialog / Project Manager / Settings (dialogs opened from menus)
- Player and Nuitka Build (external player target)

## Verified against

- `editor/main_window.py`, `editor/main_window/widgetp/frame.py`, `editor/main_window/widgetp/status.py`, `editor/main_window/widgetp/toolbar_host.py`
- `editor/main_window/toolbar.py`, `editor/main_window/menup/builder.py`, `editor/main_window/statusbar.py`, `editor/main_window/connections.py`, `editor/main_window/postinit.py`, `editor/main_window/project.py`
- `editor/main_window/handlersp/play.py`, `editor/main_window/handlersp/prefab.py`, `editor/main_window/handlersp/drop_assets.py`
