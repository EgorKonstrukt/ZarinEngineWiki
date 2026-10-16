# Файлы проекта

> Дисковые файлы проекта + настроек: `ProjectSettings.json`, `BuildSettings.json`, `_build_BuildSettings.json`, `*.import`-сайдкары, `~/.zarin/settings.json`. Везде pretty-printed JSON (`indent=2`).

[Home](../../index.md)

## `ProjectSettings.json` (корень проекта)

Читается `get_project_config(project_path)` (`core/config/config.py:318-321`) и фолбэком плеера (`player.py:92-107`: `project.default_scene`). Полная дефолтная форма (5 групп, `config.py:322-397`):

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

(Показаны headline-ключи; полные списки — в [справочнике конфига](./config-reference.md).)

## `BuildSettings.json` (корень репо/проекта)

Читается стартом редактора (`main.py:235-243`: `build_plugins`), стартом плеера (`player.py:312-320`: `build_plugins`; `player.py:68-90`: `scenes[0]` как фолбэк сцены №2) и сборкой Nuitka (`_load_build_settings`, `_get_included_scenes`, `tools/nuitka/config.py:91-117`). Форма:

```json
{
  "scenes": ["scenes/Main.zpes"],
  "build_plugins": ["physics_plugin"],
  "build_options": { "strip_unused_assets": true }
}
```

Записи `scenes[]` могут нести префикс `scenes/` (срезается при резолве); missing-файлы дают "WARNING: scene not found". `build_options.strip_unused_assets` дефолт `True`, переопределяется `--strip-unused` / `--no-strip-unused` (`tools/nuitka/build.py:build()`).

## `_build_BuildSettings.json` (образец в репо)

Зачекиненный пример в корне репо (`_build_BuildSettings.json`):

```json
{
  "build_plugins": [
    "example_plugin", "mesh_editor_plugin", "network_plugin",
    "physics_drag_plugin", "physics_plugin", "tracker_music_plugin",
    "user.bsp_import_plugin"
  ]
}
```

## `*.import`-сайдкары (per asset)

Резолв: меш — `path + ".import"` с поиском кандидатов от корня проекта (`core/assets/importer/settings.py:41-54`); текстура — `path + ".import"` с 2-сек mtime-кэшем + кэшем настроек на 1024 записи (`texture_import_settings.py:18-20,63-106`); аудио — `path + ".import"` (`clip.py:151-158`, `system.py:170-175`). Дефолты меша (`settings.py:67-77`):

```json
{
  "scale": 1.0, "center_pivot": false, "flip_uvs": true,
  "smooth_angle": 30.0, "gen_normals": true, "gen_uvs": true,
  "blendshapes": true, "materials": ["mat0"]
}
```

Дефолты текстуры (`texture_import_settings.py:42-49`):

```json
{
  "filter_mode": "trilinear", "anisotropic": 1, "max_size": 2048,
  "wrap_mode": "clamp", "compression": "none", "srgb": true
}
```

Enum в коде: `FilterMode = point|bilinear|trilinear`, `WrapMode = repeat|clamp|mirrored_repeat`, `CompressionQuality = none|low|normal|high` (`texture_import_settings.py:23-40`). Аудио-сайдкар: читается только ключ `quality` (int → целевой процент ресемпла; `>= 100` = no-op, пол 8000 Гц — `clip.py:149-169,190-199`); неподдержанные расширения бросают `ValueError("Unsupported audio format: …")` (`clip.py:160-161`).

## `~/.zarin/settings.json` (глобальный)

Путь `get_global_config()` (`config.py:142`); правится диалогом Settings; restart-ключи (тема, шрифт/ui-масштаб, язык, `engine.python_*`) применяются при следующем запуске. Битый файл → варнинг + вшитые дефолты (`config.py:93-101`); для сброса удалить файл. Полная карта 18 групп: [справочник конфига](./config-reference.md).

## Проверено по

- `core/config/config.py:142, 318-397` (пути, полные дефолты), `main.py:235-243`, `player.py:68-107, 312-320`, `tools/nuitka/config.py:91-117`, `tools/nuitka/build.py` (`strip_unused_assets`)
- `_build_BuildSettings.json` (дословный образец), `core/assets/importer/settings.py:41-106`, `core/assets/texture_import_settings.py:18-106`, `core/audio/sys/clip.py:147-199`, `core/audio/sys/system.py:170-175`
