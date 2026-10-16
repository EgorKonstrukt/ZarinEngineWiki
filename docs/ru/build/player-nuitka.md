# Плеер и сборка Nuitka

> Путь поставки: `player.py` (точка входа standalone-рантайма) + `build_nuitka.py` (тонкая обёртка над `tools/nuitka/`, все CLI-флаги — в `config.py`).

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| `player.main` | `main()` в `player.py` | Создаёт `QApplication`, резолвит сцену, поднимает движок без редактора |
| Резолвинг сцены | `_resolve_startup_scene(project_root: str) -> str`, `_deduce_project_root(scene_path, fallback)` | Путь сцены из `sys.argv[1]` либо дефолт проекта |
| Стейджинг плагинов | `_collect_plugin_jobs()`, `_split_deferred(jobs)`, `_load_deferred(engine, modules, viewport)` | Отложенная загрузка плагинов в плеер |
| Стейджинг физики | `_start_physics_load(engine)` | Фоновый прогрев физсолвера |
| Поверхность крашей | `excepthook(...)`, `_log(msg)` | Лог в `player_diag.txt` / `crash_logs/` в корне репо |
| Внешний запуск | `launch_external_player(mw)`, `_resolve_player_command(scene_path)`, `_write_player_preview(mw, project_root)` в `editor/main_window/handlersp/play.py` | Путь кнопки « Player» редактора |
| CLI сборки | `python build_nuitka.py [--editor] [--output-dir DIR] [--no-console] [--onefile] [--strip-unused \| --no-strip-unused] [--no-winrt] [--compiler auto\|mingw\|msvc] [--physics culverin\|pybullet\|physx]` | Флаги в `tools/nuitka/config.py` |
| Константы сборки | `ENTRY` (`player.py`, либо `main.py` с `--editor`), `OUTPUT_DIR` (дефолт `build_output/`), `BUILD_EDITOR`, `NO_CONSOLE`, `ONEFILE`, `COMPILER_CHOICE`, `PHYSICS_CHOICE` | `tools/nuitka/config.py` |
| Шаги сборки | `_resolve_physics_solver()`, `_setup_compiler()`, `_load_build_settings()`, `_get_included_scenes(bs)` в `config.py`; `build()` в `tools/nuitka/build.py` (+ `_minify_pil`/`_restore_pil` вокруг прогона) | Конфиг → компиляция |
| Бандлинг ассетов | `_scan_scene_assets(scene)`, `_resolve_one/_resolve_assets`, `_collect_asset_dirs`, `_plugin_include_options(names)`, `_rmtree_robust(path)` в `tools/nuitka/assets.py` | При strip едут только сцены/плагины/ассеты по ссылкам |

## Usage in Editor

Кнопка ` Player` тулбара (или `launch_external_player`) пишет preview-файл рядом с проектом и спавнит плеер на текущей сцене. `BuildDialog` собирает те же флаги, что принимает CLI (цель, компилятор, физика, onefile, консоль, strip, WinRT, выходной каталог), и выполняет сборку с окном лога.

## Usage in Code

```bash
python player.py path/to/scene.zpes
python build_nuitka.py --onefile --no-console --physics physx
python build_nuitka.py --editor --output-dir build_output
```

## Related

- Диалог сборки / менеджер проектов / настройки, Инструменты и профилирование, Менеджер плагинов (плагины в сборке)

## Verified against

- `player.py`, `build_nuitka.py`, `tools/nuitka/config.py`, `tools/nuitka/build.py`, `tools/nuitka/assets.py`, `tools/nuitka/__init__.py`
- `editor/main_window/handlersp/play.py`, `_build_BuildSettings.json`
- Примечание: `build_profiles.json` в репозитории нет — дефолты сборки живут в `tools/nuitka/config.py`, постоянные настройки проекта — в состоянии диалога Build Settings.
