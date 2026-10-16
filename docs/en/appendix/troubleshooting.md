# Troubleshooting

> Fixes for failures whose cause is visible in code. Only code-backed remedies — no speculation.

[Home](../../index.md)

## Wayland session renders nothing / window invisible

`main.py:163-177`: when `XDG_SESSION_TYPE` contains `wayland` and `QT_QPA_PLATFORM` is unset, the editor forces `QT_QPA_PLATFORM=xcb` + `QT_XCB_GL_INTEGRATION=glx` ("Falling back to XCB for OpenGL compatibility"). If `QT_QPA_PLATFORM=wayland` is set explicitly, it prints that Qt6+OpenGL on Wayland "may cause window visibility issues". Fix: unset `QT_QPA_PLATFORM` (let the fallback trigger) or export `QT_QPA_PLATFORM=xcb` yourself. Only what the code does — no compositor advice.

## Native extensions missing or stale (`.pyx` newer than `.pyd`)

`main.py:78-108` (`_check_extensions_missing`, skipped when frozen): 25 tracked module names under `core/` are compared against `core/pyx/*.pyx` mtimes — any missing `.pyd`/stale source returns True. The splash builder then runs `python setup.py build_ext --inplace` (`main.py:117-159`, progress over `total_exts = 25`). Fix: run `python setup.py build_ext --inplace` manually (add `--compiler=msvc` or `ZARIN_COMPILER=msvc` to force MSVC instead of auto-downloaded MinGW-w64; see `core/build_cython.py:18-63`). If the build fails, the splash reports "Extension build failed (exit code …)" and startup continues without natives.

## Audio silent — "OpenAL not available"

`core/audio/sys/system.py:64-66`: when the `openal` import failed at module load (`system.py:13-17`), init logs a warning "OpenAL not available, audio disabled" and runs without sound. `core/audio/efx/efx.py:15,197-203` and `filters.py:28`, `slots.py:78-82` raise `EFXError("EFX not available")` / "Failed to create …" when OpenAL/EFX is absent. Fix per code: install the OpenAL runtime + `pip install openal` bindings; EFX paths require a working EFX context (`ensure_efx`). Non-WAV formats additionally need `miniaudio` (`clip.py:164-167`: "miniaudio not available; cannot load …").

## Model import fails — "Assimp library not found"

`core/assets/importer/assimp.py:16-19,31-56`: the loader looks for `assimp-vc143-mt.dll` (win32, repo root) or `libassimp.so.6.0.5` (other OS), falling back to the bare library name on `PATH`; both binaries are shipped at repo root. On failure `_get_dll()` raises `RuntimeError("Assimp library not found")`. Fix: restore the shipped DLL/SO next to the project root (path = `_find_project_root()` walking up to the `core/` parent) or install Assimp on the loader path.

## Nuitka build fails

`tools/nuitka/build.py:build()` prints `Python:`, `Root:`, resolves scenes via `_get_included_scenes` (missing scene → "WARNING: scene not found", `config.py:116`), resolves compiler via `tools/mingwpm` ("WARNING: MinGW-w64 auto-download failed … Falling back to Nuitka's own toolchain download", `config.py:86-87`), and minifies PIL around the run (`_minify_pil` keeps Bmp/Gif/Jpeg/Ppm/Png, restores afterwards). Fix per code: retry with `--compiler msvc` (Visual Studio toolchain) or `--compiler mingw` (compact MinGW-w64); pass `--no-strip-unused` if scene-asset scanning dropped something you need; check `BuildSettings.json:scenes` paths exist.

## Black viewport (no GL context)

`main.py:206-209` and `player.py:268-271` request `QSurfaceFormat`: depth 24, OpenGL **4.6 CoreProfile**, swap interval from `rendering.vsync`. A black viewport with no exception means context creation failed on the driver side. Fix per code: verify a GL 4.6-capable driver is active (the format is non-negotiable in code); toggle `rendering.vsync` in `~/.zarin/settings.json` to rule out swap-interval issues.

## Settings corrupted — reset to defaults

Global file is `~/.zarin/settings.json` (`core/config/config.py:142`); project file is `<project>/ProjectSettings.json` (`config.py:321`). `Config.save()` writes pretty JSON (`indent=2`); a corrupt file logs "Failed to load config …" and falls back to compiled defaults (`config.py:93-101`). Fix: delete (or rename) `~/.zarin/settings.json` and restart — defaults from `get_global_config()` are re-applied; per-project, delete `ProjectSettings.json` similarly. Restart-marked keys (theme, font/ui scale, language, `engine.python_*`) only apply on next launch anyway.

## Scene file damaged — `.bak` rotation

`core/engine/eng/scenes.py:20-52`: every scene save rotates backups with `_SCENE_BACKUP_KEEP = 2` — previous `.bak` → `.bak2`, `.bak2` older than keep is deleted, current file copied to `.bak` (`shutil.copyfile`, atomic write via `.build` temp, `scenes.py:55-59`). Fix: restore `Scene.zpes` from `Scene.zpes.bak` (newest) or `Scene.zpes.bak2` (one generation older).

## Verified against

- `main.py:78-177, 206-213` (extension check/build, Wayland fallback, GL 4.6 + vsync)
- `core/build_cython.py:18-83` (MinGW default, MSVC override, flags)
- `core/audio/sys/system.py:13-17, 64-100`, `core/audio/efx/efx.py`, `filters.py`, `slots.py`, `core/audio/sys/clip.py:160-167`
- `core/assets/importer/assimp.py:16-56` (DLL/SO names, candidates, RuntimeError)
- `tools/nuitka/config.py:72-117`, `tools/nuitka/build.py` (PIL minify, warnings)
- `player.py:268-275`, `core/config/config.py:86-101, 142, 313-321`, `core/engine/eng/scenes.py:20-59`
