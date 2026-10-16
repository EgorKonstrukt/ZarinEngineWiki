# Tools Catalog

> Every script under `tools/`: purpose + launch command. `tools/nuitka/` owns the build; profiling is `py-spy` via `.bat` shims.

[Home](../../index.md)

## Gate / build

| tool | purpose | launch |
|---|---|---|
| `tools/arch_gate.py` | Architecture gate: compile, comments, docstrings, size ≤ 450 (ALLOW_BIG), undefined names, import resolvability, `ComponentRegistry` check, optional smoke suite | `py tools/arch_gate.py` / `py tools/arch_gate.py --smoke` |
| `tools/nuitka/config.py` | Owns all Nuitka CLI flags (`parser`, `ENTRY`, `OUTPUT_DIR`, `BUILD_EDITOR`, `NO_CONSOLE`, `ONEFILE`, `COMPILER_CHOICE`, `PHYSICS_CHOICE`, Assimp DLL/SO name) + physics/compiler/scene resolvers | imported by `build_nuitka.py`, not run directly |
| `tools/nuitka/build.py` | `build()`: scenes → assets → Nuitka compile, with `_minify_pil`/`_restore_pil` (keeps Bmp/Gif/Jpeg/Ppm/Png PIL plugins) | `python build_nuitka.py …` |
| `tools/nuitka/assets.py` | Scene asset scan + plugin include options (`_scan_scene_assets`, `_resolve_one/_resolve_assets`, `_collect_asset_dirs`, `_plugin_include_options`, `_rmtree_robust`) | library, used by `build.py` |
| `tools/build_plugin.py` | Plugin packaging tool: builds one plugin into a distributable binary (`.dll`/`.pyd`) via Nuitka or Cython, deps bundled (module docstring) | `python tools/build_plugin.py path/to/plugin.py [--name MyPlugin]` |
| `tools/build_zplugin.py` | `.zplugin` pack CLI (thin wrapper over `tools/zplug/`: `pack`, `cli`, `verify` — bundle, compile tree, wheels, receipts) | `python tools/build_zplugin.py …` |
| `tools/zplug/` | Pack backend package (`pack.py`, `cli.py`, `verify.py`): bundle stage, Nuitka/Cython tree compile, wheel vendor/download, install/uninstall receipts | via `build_zplugin.py` |
| `tools/mingwpm/` | Compact MinGW-w64 auto-download + activation for Cython/Nuitka (`ensure_mingw`, `activate`, `nuitka_flags`, `resolve_compiler`) | library, used by `core/build_cython.py` + `nuitka/config.py` |

## Profiling / measurement

| tool | purpose | launch |
|---|---|---|
| `tools/profile_flamegraph.bat` | `py-spy record -o profile_flamegraph.svg --subprocesses -- python main.py` | `tools\profile_flamegraph.bat` |
| `tools/profile_svg.bat` | Same with timestamped `profile_flamegraph_YYYYMMDD_HHMMSS.svg`, 30 s duration | `tools\profile_svg.bat` |
| `tools/profile_top.bat` | Live view `py-spy top --subprocesses -- python main.py` | `tools\profile_top.bat` |
| `tools/bench_disk_io.py` | Disk I/O benchmark, scratch dir `%TEMP%/zarin_disk_bench` | `python tools/bench_disk_io.py` |
| `tools/raw_disk_speed.py` | Raw disk speed via Win32 `GENERIC_READ` | `python tools/raw_disk_speed.py` |
| `tools/gpu_journal.py` | GPU journal viewer entry (`from tools.gpuj import main`) | `python tools/gpu_journal.py` |
| `tools/gpuj/` | GPU journal backend package behind `gpu_journal.py` | via `gpu_journal.py` |

## Misc

| tool | purpose | launch |
|---|---|---|
| `tools/prop_picker_smoke.py` | Standalone prop-picker smoke (mirrors `tests/smoke/test_prop_picker.py`) | `python tools/prop_picker_smoke.py` |
| `tools/qt_icons.py` | Qt icon gallery (`QApplication`, `QStyle` grid) | `python tools/qt_icons.py` |

## Verified against

- `tools/` listing (`arch_gate.py`, `bench_disk_io.py`, `build_plugin.py` docstring, `build_zplugin.py`, `gpu_journal.py:8`, `gpuj/`, `mingwpm/`, `nuitka/config.py|build.py|assets.py|__init__.py`, `profile_flamegraph.bat`, `profile_svg.bat`, `profile_top.bat`, `prop_picker_smoke.py`, `qt_icons.py`, `raw_disk_speed.py`, `zplug/`)
- `build_nuitka.py` (re-export + `build()`), `tools/nuitka/build.py` (`_minify_pil` kept set), `tools/nuitka/config.py:19-45` (flags)
