# Стайлгайд (контракт контрибуции)

> Проверяется командой `py tools/arch_gate.py`. Источники: `ARCHITECTURE.md` + `tools/arch_gate.py`.

[Home](../../index.md)

## Слои (зависимости только вниз)

```
main.py / player.py            точки входа
editor/                        редактор (PySide6): панели, вьюпорт, инспектор
plugins/                       плагины (VR, физика, трекер, plotter, MCP)
core/components/               ECS-компоненты (данные + сериализация)
core/renderer/ core/physics/
core/audio/ core/network/      подсистемы (логика, без Qt)
core/ecs/                      Entity / Component / Scene / ComponentRegistry
core/foundation/               логгер, конфиг, команды, профайлер, плагины
core/spatial/ core/maths/      математика и структуры данных (numpy float64)
```

`core/` никогда не импортирует `editor` и `plugins`; связь вверх — только через события `Engine.on/emit` и колбэки (`ARCHITECTURE.md:19`).

## Модули

- Один файл — одна ответственность, **≤ 450 строк**, один публичный класс (+ приватные хелперы). Исключение: `editor/panels/project/*` до 500 строк — allowlist `ALLOW_BIG` (`arch_gate.py:25-35,39`): `panel.py`, `pane.py`, `widgets.py`, `fs_ops.py` + вендор `plugins/tracker_music_plugin/libs/nodmod/{xmsong,modsong,song,s3msong,types}.py`.
- Сборка пакета через миксины: `component.py` (каркас) + `solver.py`/`draw.py`/… + `__init__.py` с ре-экспортом (`ARCHITECTURE.md:25`).
- Публичный API — только через `__init__.py` пакета; внутренности — приватная кухня. Канонические примеры (`ARCHITECTURE.md:27-30`): `from core.ecs import Component, Entity, Scene, ComponentRegistry`; `from core.components.transform.tr import Transform`; `from editor.panels.project import ProjectPanel`. Гейт резолвит топ-импорты против `core/editor/plugins/physics_solvers/tools/tests/playerp/nodmod/main/player` плюс `EXTENSION_SUFFIXES` (`arch_gate.py:250-274`).

## Компоненты

- Наследник `Component`; `@ComponentRegistry.register` обязателен, иначе компонент невидим (гейт проверяет все сабклассы `Component`, кроме `*Mixin*`, `*Base*`, `_приватных`, плюс `ALLOW_UNREGISTERED = {"SkyIbl", "RaytraceComponent"}` — `arch_gate.py:277-306`).
- Сериализация парой `serialize`/`deserialize`; инспектор — через `_inspector_fields`.
- Цикл: `on_awake` → `on_start` → `on_update(dt)` / `on_fixed_update(dt)` → `on_destroy` (`ARCHITECTURE.md:36`).

## Стиль кода

- Лицензия первой строкой. Комментариев нет; docstring — только если без него непонятен контракт (`ARCHITECTURE.md:40`). Гейт: `check_comments` ловит любой `#`-коммент после начала кода; `check_docstrings` ловит строковые литералы первыми стейтментами модуля/класса/функции (`arch_gate.py:88-120`). Исключения: `main.py`/`player.py`, вендор `libs/nodmod`, тесты (`ARCHITECTURE.md:42`).
- Цвета/шейдеры без `#` в исходнике — `chr(35)`, `rgb()`; рантайм-значения 1:1 (`ARCHITECTURE.md:41`, напр. `menup/builder.py:33` `_qta = lambda n, c=chr(35)+"d4d4d4": …`).
- Гейт также проверяет: `py_compile` каждого `.py` (кроме `.venv/__pycache__/NodeGraphQt/.git/build/dist/linux/~`), размер, неопределённые имена (разрешён `__compiled__`), резолвимость импортов (`arch_gate.py:42-51,123-274`).

## Проверка

```bash
py tools/arch_gate.py            # compile, комментарии, docstrings, размер, имена, импорты, реестр
py tools/arch_gate.py --smoke    # гейт + весь tests/smoke
py tests/smoke/run_smokes.py     # полный сьют отдельно (свежий процесс на тест, QT_QPA_PLATFORM=offscreen)
```

Гейт печатает `GATE OK`/`GATE FAIL` (`arch_gate.py:348`); `--smoke` терпит только `KNOWN_SMOKE_FAILS = {"test_compression_async.py", "test_prop_picker.py"}` (`arch_gate.py:38`). Предсуществующие, не регрессии (`ARCHITECTURE.md:52-54`): `test_compression_async.py` — бюджет 150 КБ невыполним на уровне `fast` (минимум 159 КБ); `test_prop_picker.py` — в `.anim`-клипе нет position-кривых, ждать `y=0.1413` не от чего.

## Проверено по

- `ARCHITECTURE.md` (слои, правило 450/500, миксины, импорты, жизненный цикл, стиль, команды гейта, 2 известных падения)
- `tools/arch_gate.py:17-39` (SKIP_TOPS, ALLOW_BIG, ALLOW_UNDEF, ALLOW_UNREGISTERED, KNOWN_SMOKE_FAILS, MAX_LINES), `:42-134` (обход, compile/комменты/docstring/размер), `:213-348` (импорты, реестр, смоки, вывод GATE)
- `editor/main_window/menup/builder.py:33` (идиома `chr(35)` для цветов)
