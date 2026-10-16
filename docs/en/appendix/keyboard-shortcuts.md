# Keyboard Shortcuts

> Complete menu shortcut map from `editor/main_window/menup/builder.py:31-190`. Viewport camera keys are stated only as far as code shows (key handler, no tooltip text found).

[Home](../../index.md)

## Menu shortcuts

| shortcut | action | menu |
|---|---|---|
| `Ctrl+N` | New Scene (`new_scene`) | File |
| `Ctrl+O` | Open Scene… (`open_scene`) | File |
| `Ctrl+S` | Save Scene (`save_scene`) | File |
| `Ctrl+Shift+S` | Save Scene As… (`save_scene_as`) | File |
| `Ctrl+Alt+S` | Save All Scenes (`_scene_tab_manager.save_all_dirty_tabs`) | File |
| `Alt+F4` | Exit (`mw.close`) | File |
| `Ctrl+Shift+N` | Create Empty (`_hierarchy._create_entity`) | GameObject |
| `Shift+F10` | Play/Stop (`toggle_play_stop`) | Game |
| `F10` | Launch in Player (`launch_external_player`) | Game |
| `Ctrl+P` | Go to Tab… (quick open; `ApplicationShortcut`) | View |
| `Ctrl+F` | Search Everywhere… (command palette; `ApplicationShortcut`) | View |
| `Ctrl+Tab` | Next Tab (`cycle_tab(1)`; `ApplicationShortcut`) | View |
| `Ctrl+Shift+Tab` | Previous Tab (`cycle_tab(-1)`; `ApplicationShortcut`) | View |
| `Ctrl+Shift+P` | Plugin Manager (`_plugin_mgr.show`) | Tools |
| `Ctrl+Shift+K` | Python Packages… (`_package_mgr.show`) | Tools |
| `Ctrl+Shift+M` | Mesh Editor (`_show_mesh_editor`) | Tools |
| `Ctrl+Shift+T` | Terrain Editor (`_show_terrain_editor`) | Tools |
| `Ctrl+Shift+G` | GUI Editor (`_gui_editor.show`) | Tools |
| `Ctrl+Shift+U` | Build Settings… (`_show_build_settings`) | Tools |
| `Ctrl+Shift+B` | Build Project… (`show_build_dialog`) | Tools |

No shortcut in code for: Undo/Redo (actions exist but disabled at rest, no `setShortcut`), Project Manager / Open Project, Add Entity…, Reset Layout, Refresh All Icons, Report Bug, About. Undo/redo start disabled (`_undo_act/_redo_act.setEnabled(False)`, `builder.py:68-75`).

## Viewport (code-backed only)

`editor/viewport/viewport_input.py:268-325`: `keyPressEvent` handles `Ctrl+C` (copy selected), `Ctrl+V` (paste), `Ctrl+D` (duplicate = copy + paste) over selected entities, then feeds keys into the input system via `_qt_key_to_vk` mapping: `W/A/S/D/Q/E/R/F`, `Shift/Ctrl/Alt`, `Delete`, `Space` (`viewport_input.py:322-325`). Mouse: `wheelEvent` → camera scroll/dolly (`viewport_input.py:261-266`); gizmo drag/orbit flags, navigation-gizmo orbit, context menu with Gizmo (Visible / Align To View-World-Local-Custom / Orientation Lock / Hide) + Create (`viewport_input.py:71,204,327-359`). Camera orbit math lives in `navgizmo/gizmo.py:133-146,262-272`. No viewport tooltip strings (`setToolTip` with WASD/RMB/MMB text) were found in `editor/viewport/` — the table above is the full honest extent; anything beyond (e.g. "RMB = fly, MMB = pan") is **not** stated in code and is omitted.

## Verified against

- `editor/main_window/menup/builder.py:31-190` (every `setShortcut(QKeySequence(…))` above; Undo/Redo without shortcuts)
- `editor/viewport/viewport_input.py:71-266, 268-325` (Ctrl+C/V/D, `_qt_key_to_vk` map, wheel), `viewport_input.py:327-359` (context menu), `editor/viewport/navgizmo/gizmo.py:133-272` (orbit)
- Honest negative: grep `setToolTip|RMB|MMB|WASD` in `editor/viewport/` yields only orbit/nav code, no control-hint tooltip
