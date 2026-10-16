# Project Files

> On-disk project + settings files: `ProjectSettings.json`, `BuildSettings.json`, `_build_BuildSettings.json`, `*.import` sidecars, `~/.zarin/settings.json`. Pretty-printed JSON (`indent=2`) everywhere.

[Home](../../index.md)

## `ProjectSettings.json` (per project root)

Read by `get_project_config(project_path)` (`core/config/config.py:318-321`) and by the player fallback (`player.py:92-107`: `project.default_scene`). Full default shape (5 groups, `config.py:322-397`):

```json
{
  "project": { "name": "Untitled", "version": "1.0.0", "default_scene": "" },
  "input": { "control_scheme": "fps", "horizontal": "a,d", "vertical": "w,s",
    "jump": "space", "fire": "mouse 0", "crouch": "left ctrl", "sprint": "left shift",
    "interact": "e", "reload": "r", "mouse_sensitivity": 1.0, "axis_dead": 0.001 },
  "rendering": { "render_pipeline": "forward", "anti_aliasing": "none", "shadow_distance": 50.0 },
  "physics": { "solver": "culverin", "physx_device": "cpu",
    "gravity_x": 0.0, "gravity_y": -9.81, "gravity_z": 0.0,
    "fixed_time_step": 0.02, "num_sub_steps": 2, "simulation_mode": "multi_threaded" },
  "audio": { "master_volume": 1.0, "music_volume": 0.8, "distance_model": "inverse_distance_clamped" }
}
```

(Only headline keys shown; complete key lists are in [Config Reference](./config-reference.md).)

## `BuildSettings.json` (repo/project root)

Read by editor startup (`main.py:235-243`: `build_plugins`), player startup (`player.py:312-320`: `build_plugins`; `player.py:68-90`: `scenes[0]` as scene fallback #2), and the Nuitka build (`_load_build_settings`, `_get_included_scenes`, `tools/nuitka/config.py:91-117`). Shape:

```json
{
  "scenes": ["scenes/Main.zpes"],
  "build_plugins": ["physics_plugin"],
  "build_options": { "strip_unused_assets": true }
}
```

`scenes[]` entries may carry a `scenes/` prefix (stripped on resolve); missing files produce "WARNING: scene not found". `build_options.strip_unused_assets` defaults `True`, overridable by `--strip-unused` / `--no-strip-unused` (`tools/nuitka/build.py:build()`).

## `_build_BuildSettings.json` (repo sample)

Checked-in example at repo root (`_build_BuildSettings.json`):

```json
{
  "build_plugins": [
    "example_plugin", "mesh_editor_plugin", "network_plugin",
    "physics_drag_plugin", "physics_plugin", "tracker_music_plugin",
    "user.bsp_import_plugin"
  ]
}
```

## `*.import` sidecars (per asset)

Resolution: mesh `path + ".import"` with project-root candidate search (`core/assets/importer/settings.py:41-54`); texture `path + ".import"` with 2 s mtime cache + 1024-entry settings cache (`texture_import_settings.py:18-20,63-106`); audio `path + ".import"` (`clip.py:151-158`, `system.py:170-175`). Mesh defaults (`settings.py:67-77`):

```json
{
  "scale": 1.0, "center_pivot": false, "flip_uvs": true,
  "smooth_angle": 30.0, "gen_normals": true, "gen_uvs": true,
  "blendshapes": true, "materials": ["mat0"]
}
```

Texture defaults (`texture_import_settings.py:42-49`):

```json
{
  "filter_mode": "trilinear", "anisotropic": 1, "max_size": 2048,
  "wrap_mode": "clamp", "compression": "none", "srgb": true
}
```

Enums in code: `FilterMode = point|bilinear|trilinear`, `WrapMode = repeat|clamp|mirrored_repeat`, `CompressionQuality = none|low|normal|high` (`texture_import_settings.py:23-40`). Audio sidecar: only key read is `quality` (int → resample target percent; `>= 100` = no-op, floor 8000 Hz — `clip.py:149-169,190-199`); unsupported extensions raise `ValueError("Unsupported audio format: …")` (`clip.py:160-161`).

## `~/.zarin/settings.json` (global)

`get_global_config()` path (`config.py:142`); edited by the Settings dialog; restart-marked keys (theme, font/ui scale, language, `engine.python_*`) apply on next launch. Corrupt file → warning + compiled defaults (`config.py:93-101`); delete the file to reset. Full 18-group map: [Config Reference](./config-reference.md).

## Verified against

- `core/config/config.py:142, 318-397` (paths, full defaults), `main.py:235-243`, `player.py:68-107, 312-320`, `tools/nuitka/config.py:91-117`, `tools/nuitka/build.py` (`strip_unused_assets`)
- `_build_BuildSettings.json` (verbatim sample), `core/assets/importer/settings.py:41-106`, `core/assets/texture_import_settings.py:18-106`, `core/audio/sys/clip.py:147-199`, `core/audio/sys/system.py:170-175`
