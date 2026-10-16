# Animation, Scripts, Prefab, Mesh, VCS Panels

> Satellite docks under `editor/panels/`: play view, animation, script editing, prefab/library, mesh editing, packages, VCS, plus the small dialogs/widgets that support them. All class/method names below are read from the listed files.

## API / Properties

| Panel | Location | Key API (verified) |
|---|---|---|
| Play dock + runtime view | `panels/play/dock.py`, `viewport.py`, `viewport_base.py`, `fallback_view.py` | In-play render surface used by Play mode |
| Animation clip editor | `panels/anim/panel.py` | Clip key editing surface |
| Animator graph editor | `panels/animator/panel.py`, `graph.py`, `params.py` | State-machine canvas + parameter list |
| Terrain dock | `panels/terrain/panel.py`, `graph_widget.py` | Embeds the terrain node canvas (see Terrain Node Graph) |
| Time travel | `panels/timetravel/panel.py`, `recorder.py`, `playback.py` | Record + scrub session history |
| VCS | `panels/vcsp/panel.py`, `git.py`, `commit.py`, `diff.py`, `widgets.py` | Git status/commit/diff inside the editor |
| Mesh editor | `panels/meshedit/panel.py`, `ops.py`, `widgets.py` | Mesh operations (backed by `plugins/mesh_editor_plugin.py` MeshEditorPlugin) |
| Script editor | `panels/script_editor_panel/panel.py` + `edit/`, `tabsp/`, `wid/` | Multi-tab code editor: `highlighter.py`, `folding.py`, `gutter.py`, `diagnostics.py`, `inspection.py`, `api_words.py`, `doc_popup.py`/`doc_provider.py`, `blame.py`, `ot.py` (collab OT), `stripe.py`, `theme.py`, `vision.py`, `smooth.py` |
| Scripts registry | `panels/scripts_panel.py`, `syntax_config.py` | Script list + syntax settings |
| Prefab editor | `panels/prefab_editor_panel.py` | Prefab tab (opened by `handlersp/prefab.py`) |
| Library packs | `panels/library_pack_dialog.py` | Pack browser dialog |
| Package manager | `panels/pkgmgr/panel.py`, `ops.py`, `bridge.py`, `dialogs.py` (+ `pkgcore/`) | Install/remove packages |
| Plugin manager | `panels/plugin_manager_panel.py`: `PluginManagerPanel(engine)` + `_refresh()`, `_toggle_plugin(row, state)`, `_load_plugin()`, `_build_plugin()`, `_package_zplugin()`, `_new_library_pack()` | Enable/disable, load, build, package |
| Plugin packager | `panels/plugin_package_panel.py` | `.zplugin` packaging UI |
| Entity creation | `panels/add_entity_menu.py`, `panels/node_palette.py` | "Add Entity..." dialog + node palette |
| Scene plumbing | `editor/scene_async.py`, `editor/scene_toolbar.py`, `editor/ipc_server.py` | Async scene load, scene toolbar, external IPC |
| App chrome | `editor/splash.py`, `editor/bug_report.py`, `editor/project_manager.py`, `editor/file_associations.py`, `editor/shellmenu/` | Splash, bug reporter, file bindings, OS shell entries |
| Helpers | `editor/cam/`, `editor/entframe/`, `editor/hover/`, `editor/gloff/`, `editor/gui_editor/`, `editor/matwiz/`, `editor/picker/`, `editor/icons.py`, `editor/constants.py`, `editor/keybinding_widget.py`, `editor/sphere_renderer.py` | Camera/entity-frame/hover helpers, GUI editor, pickers, icon + constant registries |

## Usage in Editor

Every dock toggles from the View menu and persists geometry via `load_config`. Script tabs mirror the main-window script tab bar (remote peer edits arrive through `ot.py`). The Animator panel edits states/transitions whose fields render in the Inspector animator sub-editors. Time travel records during Play and scrubs afterwards. VCS commit/diff work on the project root. No extra global hotkeys are bound in code for these docks beyond the main-window set.

## Usage in Code

```python
panel = PluginManagerPanel(engine, parent=mw)
panel._refresh()
panel._toggle_plugin(row, state)
```

## Related

- Main Window, Inspector (animator sub-editors), Project Panel, Collaboration and MCP docks
- Plugins: PluginBase Lifecycle, Plugin Manager, `.zplugin` Packaging

## Verified against

- `editor/panels/` directory listing (all names above), `editor/panels/plugin_manager_panel.py`
- `editor/scene_async.py`, `editor/scene_toolbar.py`, `editor/ipc_server.py`, `editor/splash.py`, `editor/bug_report.py`, `editor/file_associations.py`
