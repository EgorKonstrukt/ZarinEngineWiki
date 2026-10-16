# CLI Reference

> Entry points and flags: `main.py` (editor), `player.py` (runtime), `build_nuitka.py` (thin wrapper over `tools/nuitka/`), `tools/*.py` scripts.

[Home](../../index.md)

## `main.py` — editor entry

```bash
python main.py [file]
```

- `file` (positional, optional, `main.py:196`): scene file to open. When it ends with `.zpes`, the path is first offered to a running editor instance via `send_file_to_running_instance(args.file)` over TCP IPC; on success the new process exits (`main.py:198-202`). After boot, an existing `.zpes` path opens via `open_scene_by_path`, an existing `.zterr` path loads into the terrain editor (`main.py:279-287`).
- IPC: `editor/ipc_server.py` — `IpcServer.try_bind()` listens on `(IPC_HOST, IPC_PORT)` from `editor/constants.py` (`ipc_server.py:25-37`); `send_file_to_running_instance()` connects with 3 s timeout, sends `struct.pack("!I", len) + {"file": abspath}` JSON (`ipc_server.py:71-81`). Editor registers `_open_file_from_ipc` → `QTimer.singleShot(0, open_scene_by_path)` (`main.py:269-276`).
- Startup also: `_apply_python_optimization()` re-exec (`main.py:20-76`), Wayland→XCB fallback (`main.py:163-177`), GL 4.6 Core + vsync swap interval (`main.py:206-213`), splash extension build (`setup.py build_ext --inplace`, `main.py:110-159`), plugin load from `plugins/` or `BuildSettings.json:build_plugins` (`main.py:234-252`).

## `player.py` — runtime entry

```bash
python player.py [scene.zpes]
```

- Scene resolution `_resolve_startup_scene(project_root)` (`player.py:58-114`), in order: (1) CLI `sys.argv[1]` if the path exists (`player.py:62-66`); (2) `BuildSettings.json:scenes[0]`, stripping `scenes/` prefix, joined under `<root>/scenes/` (`player.py:68-90`); (3) `ProjectSettings.json:project.default_scene`, relative to project root (`player.py:92-107`); (4) `<root>/scenes/SampleScene.zpes` (`DEFAULT_SCENE`, `player.py:25`), else the bare `"SampleScene.zpes"` string (`player.py:110-114`). Project root is deduced by walking up to 5 levels for `ProjectSettings.json`/`BuildSettings.json` markers, else `/assets/` or `/scenes/` split (`player.py:117-139`).
- Plugin staging: `_collect_plugin_jobs()` scans `plugins.*` packages (`player.py:155-184`); `_split_deferred()` defers `example_plugin, mesh_editor_plugin, network_plugin, physics_drag_plugin, physics_visualisation_plugin, plotter_plugin, zarin_mcp` (`player.py:142-150,187-196`); physics module `plugins.physics_plugin` loads on a background thread `_start_physics_load` (`player.py:199-209`); deferred modules load on `QTimer.singleShot(0, _load_deferred)` with `on_scene_loaded / on_viewport_ready / on_play_start` hooks (`player.py:212-254,409-410`).
- Diagnostics: `_log()` prints + appends `player_diag.txt` at repo root (`player.py:30-42`); unhandled exceptions go to `Logger` + `player_diag.txt`, bug hooks installed (`player.py:45-55`); GL 4.6 Core, vsync from global config (`player.py:268-275`).

## `build_nuitka.py` — build wrapper

```bash
python build_nuitka.py [--editor] [--output-dir DIR] [--no-console] [--onefile]
  [--strip-unused | --no-strip-unused] [--no-winrt] [--compiler auto|mingw|msvc]
  [--physics culverin|pybullet|physx]
```

All flags are owned by `tools/nuitka/config.py:19-45` (`argparse`, `parse_known_args`); `build_nuitka.py` only re-exports and calls `build()`. `ENTRY` = `main.py` with `--editor`, else `player.py`; default `OUTPUT_DIR=build_output/` (`config.py:36-37`). Physics solver: `--physics` flag, else `ProjectSettings.json:physics.solver` normalized, fallback `culverin` (`config.py:50-69`). Compiler: `--compiler auto` → MinGW-w64 auto-download via `tools/mingwpm`, `msvc` forces Visual Studio (`config.py:72-88`). Included scenes come from `BuildSettings.json:scenes` with the same `scenes/`-prefix strip and missing-scene warning (`config.py:99-117`). `tools/nuitka/build.py:build()` compiles, with `_minify_pil`/`_restore_pil` around the run (keeps 5 PIL plugins: Bmp, Gif, Jpeg, Ppm, Png). Asset bundling helpers live in `tools/nuitka/assets.py` (`_scan_scene_assets`, `_resolve_one/_resolve_assets`, `_collect_asset_dirs`, `_plugin_include_options`, `_rmtree_robust`).

## `tools/*.py` launch

```bash
py tools/arch_gate.py            # architecture gate
py tools/arch_gate.py --smoke    # gate + full tests/smoke
py tests/smoke/run_smokes.py     # smoke suite, fresh process per test, QT_QPA_PLATFORM=offscreen
python setup.py build_ext --inplace [--compiler=msvc]  # rebuild Cython extensions
python tools/build_plugin.py path/to/plugin.py [--name MyPlugin]  # plugin binary packaging
python tools/build_zplugin.py ...  # .zplugin packs (wraps tools/zplug/)
python tools/bench_disk_io.py    # disk I/O benchmark (tmp: %TEMP%/zarin_disk_bench)
python tools/raw_disk_speed.py    # raw disk speed (Win32 GENERIC_READ)
python tools/gpu_journal.py      # GPU journal viewer (delegates to tools/gpuj:main)
python tools/prop_picker_smoke.py
python tools/qt_icons.py         # Qt icon gallery
profile_flamegraph.bat / profile_svg.bat  # py-spy record -o *.svg --subprocesses -- python main.py
profile_top.bat                  # py-spy top --subprocesses -- python main.py
```

## Verified against

- `main.py:193-202, 279-287` (file arg, IPC forward, `.zpes`/`.zterr` open), `editor/ipc_server.py` (TCP protocol, timeout, bind)
- `player.py:25, 58-139` (resolution order, `DEFAULT_SCENE`, root deduction), `player.py:142-254, 409-410` (plugin staging)
- `build_nuitka.py`, `tools/nuitka/config.py` (all CLI flags, ENTRY/OUTPUT_DIR, physics/compiler resolution, scenes), `tools/nuitka/build.py`, `tools/nuitka/assets.py`, `tools/nuitka/__init__.py`
- `tools/` directory listing + `tools/profile_*.bat`, `tools/bench_disk_io.py`, `tools/raw_disk_speed.py`, `tools/gpu_journal.py`, `tools/build_plugin.py` (docstring), `tools/build_zplugin.py`, `tools/gpuj/`, `tools/mingwpm/`, `tools/zplug/`, `tools/qt_icons.py`, `tools/prop_picker_smoke.py`
