# Справочник CLI

> Точки входа и флаги: `main.py` (редактор), `player.py` (рантайм), `build_nuitka.py` (тонкая обёртка над `tools/nuitka/`), скрипты `tools/*.py`.

[Home](../../index.md)

## `main.py` — вход редактора

```bash
python main.py [file]
```

- `file` (позиционный, опциональный, `main.py:196`): файл сцены для открытия. При суффиксе `.zpes` путь сначала предлагается запущенному экземпляру редактора через `send_file_to_running_instance(args.file)` по TCP IPC; при успехе новый процесс завершается (`main.py:198-202`). После старта существующий `.zpes` открывается через `open_scene_by_path`, существующий `.zterr` грузится в редактор террейна (`main.py:279-287`).
- IPC: `editor/ipc_server.py` — `IpcServer.try_bind()` слушает `(IPC_HOST, IPC_PORT)` из `editor/constants.py` (`ipc_server.py:25-37`); `send_file_to_running_instance()` коннектится с таймаутом 3 с, шлёт `struct.pack("!I", len) + {"file": abspath}` JSON (`ipc_server.py:71-81`). Редактор регистрирует `_open_file_from_ipc` → `QTimer.singleShot(0, open_scene_by_path)` (`main.py:269-276`).
- Стартап также: перевыполнение `_apply_python_optimization()` (`main.py:20-76`), фолбэк Wayland→XCB (`main.py:163-177`), GL 4.6 Core + vsync swap interval (`main.py:206-213`), сборка расширений через сплэш (`setup.py build_ext --inplace`, `main.py:110-159`), загрузка плагинов из `plugins/` или `BuildSettings.json:build_plugins` (`main.py:234-252`).

## `player.py` — вход рантайма

```bash
python player.py [scene.zpes]
```

- Резолв сцены `_resolve_startup_scene(project_root)` (`player.py:58-114`) по порядку: (1) CLI `sys.argv[1]`, если путь существует (`player.py:62-66`); (2) `BuildSettings.json:scenes[0]` со strip префикса `scenes/`, join под `<root>/scenes/` (`player.py:68-90`); (3) `ProjectSettings.json:project.default_scene` относительно корня проекта (`player.py:92-107`); (4) `<root>/scenes/SampleScene.zpes` (`DEFAULT_SCENE`, `player.py:25`), иначе голая строка `"SampleScene.zpes"` (`player.py:110-114`). Корень проекта выводится подъёмом до 5 уровней до маркеров `ProjectSettings.json`/`BuildSettings.json`, иначе сплит по `/assets/` или `/scenes/` (`player.py:117-139`).
- Стейджинг плагинов: `_collect_plugin_jobs()` сканирует пакеты `plugins.*` (`player.py:155-184`); `_split_deferred()` откладывает `example_plugin, mesh_editor_plugin, network_plugin, physics_drag_plugin, physics_visualisation_plugin, plotter_plugin, zarin_mcp` (`player.py:142-150,187-196`); модуль физики `plugins.physics_plugin` грузится в фоновом потоке `_start_physics_load` (`player.py:199-209`); отложенные модули грузятся по `QTimer.singleShot(0, _load_deferred)` с хуками `on_scene_loaded / on_viewport_ready / on_play_start` (`player.py:212-254,409-410`).
- Диагностика: `_log()` печатает + дописывает `player_diag.txt` в корне репо (`player.py:30-42`); необработанные исключения — в `Logger` + `player_diag.txt`, баг-хуки установлены (`player.py:45-55`); GL 4.6 Core, vsync из глобального конфига (`player.py:268-275`).

## `build_nuitka.py` — обёртка сборки

```bash
python build_nuitka.py [--editor] [--output-dir DIR] [--no-console] [--onefile]
  [--strip-unused | --no-strip-unused] [--no-winrt] [--compiler auto|mingw|msvc]
  [--physics culverin|pybullet|physx]
```

Все флаги принадлежат `tools/nuitka/config.py:19-45` (`argparse`, `parse_known_args`); `build_nuitka.py` только реэкспортит и вызывает `build()`. `ENTRY` = `main.py` с `--editor`, иначе `player.py`; дефолт `OUTPUT_DIR=build_output/` (`config.py:36-37`). Физсолвер: флаг `--physics`, иначе `ProjectSettings.json:physics.solver` с нормализацией, фолбэк `culverin` (`config.py:50-69`). Компилятор: `--compiler auto` → MinGW-w64 автозагрузка через `tools/mingwpm`, `msvc` форсирует Visual Studio (`config.py:72-88`). Сцены в сборку — из `BuildSettings.json:scenes` с тем же strip префикса `scenes/` и варнингом о missing-сцене (`config.py:99-117`). `tools/nuitka/build.py:build()` компилирует, вокруг — `_minify_pil`/`_restore_pil` (оставляет 5 PIL-плагинов: Bmp, Gif, Jpeg, Ppm, Png). Хелперы бандлинга ассетов — в `tools/nuitka/assets.py` (`_scan_scene_assets`, `_resolve_one/_resolve_assets`, `_collect_asset_dirs`, `_plugin_include_options`, `_rmtree_robust`).

## Запуск `tools/*.py`

```bash
py tools/arch_gate.py            # архитектурный гейт
py tools/arch_gate.py --smoke    # гейт + весь tests/smoke
py tests/smoke/run_smokes.py     # сьют смоков, свежий процесс на тест, QT_QPA_PLATFORM=offscreen
python setup.py build_ext --inplace [--compiler=msvc]  # пересборка Cython-расширений
python tools/build_plugin.py path/to/plugin.py [--name MyPlugin]  # упаковка плагина в бинарник
python tools/build_zplugin.py ...  # .zplugin-паки (обёртка над tools/zplug/)
python tools/bench_disk_io.py    # бенчмарк диска (tmp: %TEMP%/zarin_disk_bench)
python tools/raw_disk_speed.py    # скорость диска (Win32 GENERIC_READ)
python tools/gpu_journal.py      # просмотр GPU-журнала (делегирует tools/gpuj:main)
python tools/prop_picker_smoke.py
python tools/qt_icons.py         # галерея Qt-иконок
profile_flamegraph.bat / profile_svg.bat  # py-spy record -o *.svg --subprocesses -- python main.py
profile_top.bat                  # py-spy top --subprocesses -- python main.py
```

## Проверено по

- `main.py:193-202, 279-287` (аргумент file, IPC-форвард, открытие `.zpes`/`.zterr`), `editor/ipc_server.py` (TCP-протокол, таймаут, bind)
- `player.py:25, 58-139` (порядок резолва, `DEFAULT_SCENE`, вывод корня), `player.py:142-254, 409-410` (стейджинг плагинов)
- `build_nuitka.py`, `tools/nuitka/config.py` (все CLI-флаги, ENTRY/OUTPUT_DIR, резолв physics/компилятора, сцены), `tools/nuitka/build.py`, `tools/nuitka/assets.py`, `tools/nuitka/__init__.py`
- Листинг `tools/` + `tools/profile_*.bat`, `tools/bench_disk_io.py`, `tools/raw_disk_speed.py`, `tools/gpu_journal.py`, `tools/build_plugin.py` (docstring), `tools/build_zplugin.py`, `tools/gpuj/`, `tools/mingwpm/`, `tools/zplug/`, `tools/qt_icons.py`, `tools/prop_picker_smoke.py`
