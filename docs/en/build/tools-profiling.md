# Tools and Profiling Scripts

> Repo utilities under `tools/`: architecture gate, disk-I/O benches, offline profiler launchers, plugin packagers, GPU-journal and compiler helpers. Scripts run from the repo root; nothing here ships in the player.

## API / Properties

| Tool | Entry | Notes |
|---|---|---|
| Architecture gate | `tools/arch_gate.py`: `check_compile(files)`, `check_comments(files)`, `check_docstrings(files)`, `check_size(files)`, `check_undefined(files)` (+ `_Loads`/`_TrySkip` AST visitors) | Static policy checks over changed files |
| Disk bench | `tools/bench_disk_io.py`: `make_blob(path, nbytes)`, `bench_raw_single(path)`, `bench_raw_parallel(path, nthreads)`, `make_big_ply(path, nverts=2_000_000)`, `make_big_obj(path, nverts=2_000_000)`, `main()` | Blob/PLY/OBJ throughput harness |
| Raw speed probe | `tools/raw_disk_speed.py` | Quick sequential-speed probe |
| Offline CPU profiles | `tools/profile_top.bat`, `tools/profile_svg.bat`, `tools/profile_flamegraph.bat` | Top/flamegraph/SVG capture launchers |
| GPU journal | `tools/gpu_journal.py` | GPU-side event journal helper |
| Plugin builders | `tools/build_plugin.py`, `tools/build_zplugin.py`, `tools/zplug/` | Package/build helpers behind the Plugin Manager panel buttons |
| Compiler env | `tools/mingwpm/`, `tools/gpuj/` | MinGW and GPU-journal support dirs |
| Smoke checks | `tools/prop_picker_smoke.py`, `tools/qt_icons.py` | Property-picker smoke test, Qt icon audit |

## Usage in Editor

The Profiler dock covers live frames; these scripts cover offline work: run the `.bat` launchers to capture CPU profiles, `bench_disk_io.py` to compare disk throughput before tuning streaming chunk sizes, `arch_gate.py` in CI/pre-commit to block policy violations. The Plugin Manager panel's Build/Package buttons shell out through the `build_plugin`/`build_zplugin` helpers.

## Usage in Code

```bash
python tools/arch_gate.py
python tools/bench_disk_io.py
tools/profile_top.bat
python tools/build_zplugin.py --help
```

## Related

- Profiler Panel and Undo History (live profiling), Player and Nuitka Build (packaging), Plugin Manager (build/package buttons)

## Verified against

- `tools/arch_gate.py`, `tools/bench_disk_io.py`, `tools/raw_disk_speed.py`, `tools/gpu_journal.py`, `tools/build_plugin.py`, `tools/build_zplugin.py`, `tools/prop_picker_smoke.py`, `tools/qt_icons.py`
- `tools/profile_top.bat`, `tools/profile_svg.bat`, `tools/profile_flamegraph.bat`, `tools/zplug/`, `tools/gpuj/`, `tools/mingwpm/`, `tools/nuitka/`
