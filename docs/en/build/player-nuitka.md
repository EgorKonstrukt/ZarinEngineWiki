# Player and Nuitka Build

> Ship path: `player.py` (standalone runtime entry) + `build_nuitka.py` (thin wrapper over `tools/nuitka/`, whose `config.py` owns all CLI flags).

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `player.main` | `main()` in `player.py` | Creates `QApplication`, resolves scene, boots engine headless-of-editor |
| Scene resolution | `_resolve_startup_scene(project_root: str) -> str`, `_deduce_project_root(scene_path, fallback)` | `sys.argv[1]` scene path or project default |
| Plugin staging | `_collect_plugin_jobs()`, `_split_deferred(jobs)`, `_load_deferred(engine, modules, viewport)` | Deferred plugin load into the player |
| Physics staging | `_start_physics_load(engine)` | Background physics-solver warmup thread |
| Crash surface | `excepthook(...)`, `_log(msg)` | Logs to `player_diag.txt` / `crash_logs/` at repo root |
| External launch | `launch_external_player(mw)`, `_resolve_player_command(scene_path)`, `_write_player_preview(mw, project_root)` in `editor/main_window/handlersp/play.py` | Editor " Player" button path |
| Build CLI | `python build_nuitka.py [--editor] [--output-dir DIR] [--no-console] [--onefile] [--strip-unused \| --no-strip-unused] [--no-winrt] [--compiler auto\|mingw\|msvc] [--physics culverin\|pybullet\|physx]` | Flags in `tools/nuitka/config.py` |
| Build constants | `ENTRY` (`player.py`, or `main.py` with `--editor`), `OUTPUT_DIR` (default `build_output/`), `BUILD_EDITOR`, `NO_CONSOLE`, `ONEFILE`, `COMPILER_CHOICE`, `PHYSICS_CHOICE` | `tools/nuitka/config.py` |
| Build steps | `_resolve_physics_solver()`, `_setup_compiler()`, `_load_build_settings()`, `_get_included_scenes(bs)` in `config.py`; `build()` in `tools/nuitka/build.py` (+ `_minify_pil`/`_restore_pil` around the run) | Config → compile |
| Asset bundling | `_scan_scene_assets(scene)`, `_resolve_one/_resolve_assets`, `_collect_asset_dirs`, `_plugin_include_options(names)`, `_rmtree_robust(path)` in `tools/nuitka/assets.py` | Only referenced scenes/plugins/assets ship when stripping |

## Usage in Editor

Toolbar ` Player` button (or `launch_external_player`) writes a preview file next to the project and spawns the player against the current scene. `BuildDialog` collects the same flags the CLI accepts (target, compiler, physics, onefile, console, strip, WinRT, output dir) and runs the build with a log view.

## Usage in Code

```bash
python player.py path/to/scene.zpes
python build_nuitka.py --onefile --no-console --physics physx
python build_nuitka.py --editor --output-dir build_output
```

## Related

- Build Dialog / Project Manager / Settings, Tools and Profiling, Plugin Manager (plugins-in-build)

## Verified against

- `player.py`, `build_nuitka.py`, `tools/nuitka/config.py`, `tools/nuitka/build.py`, `tools/nuitka/assets.py`, `tools/nuitka/__init__.py`
- `editor/main_window/handlersp/play.py`, `_build_BuildSettings.json`
- Note: no `build_profiles.json` exists in the repo — build defaults live in `tools/nuitka/config.py`, persisted per-project settings in Build Settings dialog state.
