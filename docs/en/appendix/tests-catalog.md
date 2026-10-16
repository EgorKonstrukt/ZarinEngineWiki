# Tests Catalog

> Every `tests/smoke/test_*.py` (16 files) in one line each, plus the runners. All smokes are standalone scripts (`sys.path.insert` of repo root) executed each in a fresh process with `QT_QPA_PLATFORM=offscreen` (`run_smokes.py:9-23`).

[Home](../../index.md)

## Smoke tests (`tests/smoke/`)

| file | what it drives |
|---|---|
| `test_blendshapes.py` | Synthetic `MeshData` + `BlendShapes` deform path (imports `core.renderer.meshd`, `blendshapes`) |
| `test_compression_async.py` | Async scene resource compression vs 150 KB `fast`-level budget — **known fail** (minimum 159 KB, `ARCHITECTURE.md:52-53`) |
| `test_digest_dedup.py` | Embed digest/dedup (`core.ecs.embed`) round-trip in a temp dir |
| `test_duplicate_skinned.py` | Skinned-mesh duplication (`Transform`, `skinned_factory`, armature) |
| `test_embed2.py` | `embed_scene_resources` / `extract_embedded_resources` with `.bak` file moves |
| `test_embed_outside.py` | Embed with resources outside project root (`_storage_key`, `_iter_component_path_entries`) |
| `test_engine_embed.py` | Full `Engine` + `MeshFilter` embed flow with `.bak` moves |
| `test_ik_numeric.py` | Numerics of `two_bone_flat` / `fabrik_flat` from `core._ik` |
| `test_import_materials.py` | `MeshRenderer` + asset material import pipeline |
| `test_mesh_import_persist.py` | `quad.obj` mesh import persistence under a temp `proj/assets/meshes` root |
| `test_prop_picker.py` | Prop-picker against an `.anim` clip — **known fail** (no position curves, `y=0.1413` never fires, `ARCHITECTURE.md:54`) |
| `test_relpaths.py` | Relative-path handling for `quad.obj` + materials under temp `proj/` |
| `test_script_cython.py` | User-script Cython companion build (`cythonp`, `ScriptComponent`) in temp dir |
| `test_script_inspector.py` | Script inspector widgets offscreen (`QApplication`, `QPushButton/QLineEdit/QSlider`) |
| `test_scripting.py` | `Engine` + `Scene` + `ScriptComponent` script lifecycle |
| `test_soft_remote.py` | Headless soft-body round-trip in the separate physics process |

## Runners

- `tests/smoke/run_smokes.py` — iterates `SMOKE_DIR.glob("test_*.py")` sorted, `subprocess.run([PYTHON, script], cwd=ROOT, PYTHONPATH=ROOT, QT_QPA_PLATFORM=offscreen)`, prints `== name` / `PASS` / `FAIL (exit N)`, summary `FAILED: …` or `ALL N SMOKE TESTS PASSED`, exit 1/0 (`run_smokes.py:19-38`). Launch: `py tests/smoke/run_smokes.py`.
- `tools/arch_gate.py --smoke` — same suite via `run_smokes()` subprocess, parses `FAILED:` lines, tolerates only `KNOWN_SMOKE_FAILS = {"test_compression_async.py", "test_prop_picker.py"}` (`arch_gate.py:309-318,340-346`).
- `tests/libs/test_library_packs.py` — library-pack suite (zplug packs; imports `json/os/sys/tempfile` at head). Launch: `python tests/libs/test_library_packs.py`.

## Adding your own test

1. Create `tests/smoke/test_<name>.py` as a standalone script: insert repo root on `sys.path` (copy the `sys.path.insert(0, r"…ZarinEngine")` preamble), guard Qt with `os.environ.setdefault("QT_QPA_PLATFORM", "offscreen")` before `QApplication`.
2. Exit `0` on success, non-zero on failure — the runner only checks `returncode` (`run_smokes.py:27-33`).
3. Keep engine-touching tests hermetic: use `tempfile.mkdtemp(prefix=…)` project roots like `test_mesh_import_persist.py` / `test_relpaths.py`; move fixtures to `*.bak` (not delete) if you need restore, mirroring `test_engine_embed.py:67-70`.
4. New files under `tests/` are skipped by the arch gate's style scope (`arch_gate.py:72`) but still run under `--smoke`; if your test fails for a pre-existing reason, do not extend `KNOWN_SMOKE_FAILS` without updating `ARCHITECTURE.md:52-54`.

## Verified against

- `tests/smoke/` directory listing (16 `test_*.py` + `run_smokes.py`), head of each test file (imports/first lines), `tests/smoke/run_smokes.py` (env, loop, exit codes)
- `tools/arch_gate.py:38,309-346` (KNOWN_SMOKE_FAILS, smoke parsing), `ARCHITECTURE.md:46-54` (gate commands, 2 known failures)
- `tests/libs/test_library_packs.py` (head), `tools/prop_picker_smoke.py` (related picker smoke)
