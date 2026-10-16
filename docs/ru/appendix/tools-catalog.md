# Каталог инструментов

> Каждый скрипт под `tools/`: назначение + команда запуска. Сборкой владеет `tools/nuitka/`; профилирование — `py-spy` через `.bat`-шимы.

[Home](../../index.md)

## Гейт / сборка

| инструмент | назначение | запуск |
|---|---|---|
| `tools/arch_gate.py` | Архгейт: compile, комменты, docstrings, размер ≤ 450 (ALLOW_BIG), неопределённые имена, резолвимость импортов, проверка `ComponentRegistry`, опционально сьют смоков | `py tools/arch_gate.py` / `py tools/arch_gate.py --smoke` |
| `tools/nuitka/config.py` | Владеет всеми CLI-флагами Nuitka (`parser`, `ENTRY`, `OUTPUT_DIR`, `BUILD_EDITOR`, `NO_CONSOLE`, `ONEFILE`, `COMPILER_CHOICE`, `PHYSICS_CHOICE`, имя Assimp DLL/SO) + резолверы physics/компилятора/сцен | импортируется `build_nuitka.py`, напрямую не запускается |
| `tools/nuitka/build.py` | `build()`: сцены → ассеты → компиляция Nuitka, вокруг `_minify_pil`/`_restore_pil` (оставляет PIL-плагины Bmp/Gif/Jpeg/Ppm/Png) | `python build_nuitka.py …` |
| `tools/nuitka/assets.py` | Скан ассетов сцен + plugin include options (`_scan_scene_assets`, `_resolve_one/_resolve_assets`, `_collect_asset_dirs`, `_plugin_include_options`, `_rmtree_robust`) | библиотека, используется `build.py` |
| `tools/build_plugin.py` | Упаковщик плагинов: собирает один плагин в распространяемый бинарник (`.dll`/`.pyd`) через Nuitka или Cython, зависимости вшиты (docstring модуля) | `python tools/build_plugin.py path/to/plugin.py [--name MyPlugin]` |
| `tools/build_zplugin.py` | CLI `.zplugin`-паков (тонкая обёртка над `tools/zplug/`: `pack`, `cli`, `verify` — стейдж, компиляция дерева, колёса, ресипты) | `python tools/build_zplugin.py …` |
| `tools/zplug/` | Бэкенд паков (`pack.py`, `cli.py`, `verify.py`): bundle-стейдж, компиляция дерева Nuitka/Cython, вендор/загрузка колёс, install/uninstall ресиптов | через `build_zplugin.py` |
| `tools/mingwpm/` | Автозагрузка + активация компактного MinGW-w64 для Cython/Nuitka (`ensure_mingw`, `activate`, `nuitka_flags`, `resolve_compiler`) | библиотека, используется `core/build_cython.py` + `nuitka/config.py` |

## Профилирование / замеры

| инструмент | назначение | запуск |
|---|---|---|
| `tools/profile_flamegraph.bat` | `py-spy record -o profile_flamegraph.svg --subprocesses -- python main.py` | `tools\profile_flamegraph.bat` |
| `tools/profile_svg.bat` | То же с таймстампом `profile_flamegraph_YYYYMMDD_HHMMSS.svg`, длительность 30 с | `tools\profile_svg.bat` |
| `tools/profile_top.bat` | Лайв `py-spy top --subprocesses -- python main.py` | `tools\profile_top.bat` |
| `tools/bench_disk_io.py` | Бенчмарк дискового I/O, скратч `%TEMP%/zarin_disk_bench` | `python tools/bench_disk_io.py` |
| `tools/raw_disk_speed.py` | Скорость диска через Win32 `GENERIC_READ` | `python tools/raw_disk_speed.py` |
| `tools/gpu_journal.py` | Просмотр GPU-журнала (`from tools.gpuj import main`) | `python tools/gpu_journal.py` |
| `tools/gpuj/` | Бэкенд GPU-журнала за `gpu_journal.py` | через `gpu_journal.py` |

## Прочее

| инструмент | назначение | запуск |
|---|---|---|
| `tools/prop_picker_smoke.py` | Отдельный смок проп-пикера (зеркало `tests/smoke/test_prop_picker.py`) | `python tools/prop_picker_smoke.py` |
| `tools/qt_icons.py` | Галерея Qt-иконок (`QApplication`, сетка `QStyle`) | `python tools/qt_icons.py` |

## Проверено по

- Листинг `tools/` (`arch_gate.py`, `bench_disk_io.py`, `build_plugin.py` docstring, `build_zplugin.py`, `gpu_journal.py:8`, `gpuj/`, `mingwpm/`, `nuitka/config.py|build.py|assets.py|__init__.py`, `profile_flamegraph.bat`, `profile_svg.bat`, `profile_top.bat`, `prop_picker_smoke.py`, `qt_icons.py`, `raw_disk_speed.py`, `zplug/`)
- `build_nuitka.py` (реэкспорт + `build()`), `tools/nuitka/build.py` (kept-сет `_minify_pil`), `tools/nuitka/config.py:19-45` (флаги)
