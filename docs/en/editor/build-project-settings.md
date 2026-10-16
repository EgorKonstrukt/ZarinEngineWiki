# Build Dialog, Project Manager, Settings

> Shipping surface: `BuildDialog` (one-shot Nuitka build), `BuildSettingsDialog` (persistent scenes/plugins/strip selection), `ProjectManager` dialog, Global/Project settings dialogs (`editor/settings/`).

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `BuildDialog` | `(parent=None)`, title "Build Zarin Engine" | `editor/build_dialog.py` |
| Build targets | `_target_combo` (player/editor), `_compiler_combo`, `_physics_combo` | Mirrors `tools/nuitka/config.py` choices |
| Build flags | `_onefile_cb` ("Single file (--onefile)"), `_console_cb` ("Show console window"), `_strip_cb` ("Strip unused assets..."), `_winrt_cb` ("Include Windows Runtime DLLs...") | Collected by `_collect_args()` |
| Build run | `_start_build()`, `_run_build(args)`, `_on_build_done(returncode)`, `_cancel_build()`, `_browse_output_dir()`, `_open_output_folder()`, `_append_log(text)` | Async build with log view |
| `BuildSettingsDialog` | `(parent=None)`, title "Build Settings" | `editor/build_settings_dialog.py`; Save/Cancel persist |
| Settings groups | "Scenes In Build" list, "Plugins In Build" checklist, "Build Options" group | Scenes picked from `<project>/scenes`, plugins from `<project>/plugins` |
| `_strip_check` | `"Strip unused assets (scan scenes for references)"` | Persistent strip toggle |
| `ProjectManager` | `editor/project_manager.py` | Project create/open dialog |
| Settings | `editor/settings/` | Global Settings... / Project Settings... dialogs (Edit menu) |

## Usage in Editor

File/Edit menus open Project Manager, Global/Project Settings. Build flow: "Build Settings..." checks the scenes and plugins to ship and the strip toggle (saved per project); "Build..." opens `BuildDialog`, picks target (player `player.py` vs editor `main.py`), compiler (auto/mingw/msvc), physics solver (culverin/pybullet/physx), onefile/console/strip/WinRT flags, output dir — then builds with live log and open-output-folder on success.

## Usage in Code

```python
dlg = BuildDialog(parent=mw)
dlg._start_build()   # collects _collect_args() and spawns the build
```

```python
s = BuildSettingsDialog(project_root, parent=mw)
# Save persists scenes-in-build, plugins-in-build, strip flag
```

## Related

- Player and Nuitka Build (CLI behind the dialog), Tools and Profiling, Plugin Manager panel (build/package buttons)

## Verified against

- `editor/build_dialog.py`, `editor/build_settings_dialog.py`, `editor/project_manager.py`, `editor/settings/`
- `tools/nuitka/config.py` (flag parity), `_build_BuildSettings.json` (repo-root sample settings)
