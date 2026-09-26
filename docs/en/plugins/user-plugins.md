# User Plugins Guide

Team tools live in `plugins/user/` as `.py` files: importers, level utilities, batch renamers, experiment rigs, custom panels. System tier stays untouched; user tier iterates freely.

Scaffold, step by step:

1. Copy `plugins/example_plugin.py` to `plugins/user/my_tool.py`.
2. Rename the class and `NAME` (manager key — unique per plugin), bump `VERSION`, write a one-line `DESCRIPTION`, keep `SYSTEM = False`.
3. Implement `initialize(engine)`: menu items, toolbar buttons, docks, file openers. Defer project-dependent setup to `on_project_opened` — `initialize` runs before the project root is known.
4. Add behavior hooks: `step` for per-frame tools, `on_scene_loaded` for scene scanners, `on_play_start/stop` for session rigs.
5. Persist with `get_config`/`set_config` — window layouts, last paths, counters survive restarts with zero file code.
6. Restart the editor (or trigger plugin rescan from the manager panel) and verify: menu entry, button, console lines.

Patching etiquette: extend components with `add_inspector_field` and `patch_component` instead of editing core files — your fields ride engine updates instead of conflicting with them. Everything unpatches on shutdown automatically.

Debugging: console first (import errors name the missing module and line), manager panel second (load state per plugin), `Logger.debug` breadcrumbs third. A plugin that raises in `initialize` is skipped with a log — the editor always starts.

Distribution inside a team: share the `.py` file directly for trusted colleagues; ship `.zplugin` for everyone else (signed, versioned, dependency-checked — see Packaging).

Anti-patterns: blocking network calls in `initialize` (defer to hooks or threads), duplicate `NAME`s across files (second load swaps the first), absolute paths in configs (store project-relative), per-frame allocations in `step` (cache in `initialize`).

Related: Example Plugin walkthrough (conventions), PluginBase Lifecycle (hooks), .zplugin Packaging (distribution), Plugin Manager panel (Editor Manual).
