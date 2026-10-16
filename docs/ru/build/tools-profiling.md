# Инструменты и скрипты профилирования

> Утилиты репозитория в `tools/`: архитектурный гейт, бенчи диск-I/O, офлайн-лаунчеры профайлера, упаковщики плагинов, хелперы GPU-журнала и компилятора. Скрипты запускаются из корня репо; в плеер ничего отсюда не едет.

## API / Properties

| Инструмент | Точка входа | Примечания |
|---|---|---|
| Архитектурный гейт | `tools/arch_gate.py`: `check_compile(files)`, `check_comments(files)`, `check_docstrings(files)`, `check_size(files)`, `check_undefined(files)` (+ AST-визиторы `_Loads`/`_TrySkip`) | Статические проверки по изменённым файлам |
| Бенч диска | `tools/bench_disk_io.py`: `make_blob(path, nbytes)`, `bench_raw_single(path)`, `bench_raw_parallel(path, nthreads)`, `make_big_ply(path, nverts=2_000_000)`, `make_big_obj(path, nverts=2_000_000)`, `main()` | Х harness пропускной способности на blob/PLY/OBJ |
| Быстрый замер | `tools/raw_disk_speed.py` | Короткая проба последовательной скорости |
| Офлайн CPU-профили | `tools/profile_top.bat`, `tools/profile_svg.bat`, `tools/profile_flamegraph.bat` | Лаунчеры захвата top/flamegraph/SVG |
| GPU-журнал | `tools/gpu_journal.py` | Хелпер событийного журнала GPU |
| Сборщики плагинов | `tools/build_plugin.py`, `tools/build_zplugin.py`, `tools/zplug/` | Хелперы за кнопками Build/Package панели менеджера плагинов |
| Окружение компилятора | `tools/mingwpm/`, `tools/gpuj/` | Каталоги поддержки MinGW и GPU-журнала |
| Смоук-проверки | `tools/prop_picker_smoke.py`, `tools/qt_icons.py` | Смоук-тест пикера свойств, аудит Qt-иконок |

## Usage in Editor

Док профайлера покрывает живые кадры; эти скрипты — для офлайн-работы: `.bat`-лаунчеры снимают CPU-профили, `bench_disk_io.py` сравнивает пропускную способность диска перед настройкой размеров стриминговых чанков, `arch_gate.py` в CI/pre-commit блокирует нарушения политик. Кнопки Build/Package панели менеджера плагинов ходят в шелл через хелперы `build_plugin`/`build_zplugin`.

## Usage in Code

```bash
python tools/arch_gate.py
python tools/bench_disk_io.py
tools/profile_top.bat
python tools/build_zplugin.py --help
```

## Related

- Панель профайлера и история Undo (живое профилирование), Плеер и сборка Nuitka (упаковка), Менеджер плагинов (кнопки build/package)

## Verified against

- `tools/arch_gate.py`, `tools/bench_disk_io.py`, `tools/raw_disk_speed.py`, `tools/gpu_journal.py`, `tools/build_plugin.py`, `tools/build_zplugin.py`, `tools/prop_picker_smoke.py`, `tools/qt_icons.py`
- `tools/profile_top.bat`, `tools/profile_svg.bat`, `tools/profile_flamegraph.bat`, `tools/zplug/`, `tools/gpuj/`, `tools/mingwpm/`, `tools/nuitka/`
