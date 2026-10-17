# Отладка скриптов

Куда уходят ошибки, как проверить скрипт до Play и что рантайм от вас скрывает.

## Кнопка Check (редактор скриптов)

Кнопка тулбара **Check** (дубль в меню `Run → Check Script`):

| Место | Поведение |
|---|---|
| `editor/panels/script_editor_panel/wid/widget.py:196-197` | `QAction("Check")`, подсказка `"Check script for errors"` → `_check_current()`. |
| `editor/panels/script_editor_panel/wid/tabs_host.py:135-157` | `_check_current()`: читает живую диагностику текущей вкладки и показывает message box: `No errors or warnings found.` если чисто; иначе `error [line N]: message` (первые 15) + `warning [line N]: message` (первые 15). Статус-лейбл: `✖ E ⚠ W` / `⚠ W` / `✓ clean`. |

Это редакторный статический анализ. Ниже — вторая сеть, рантаймная.

## Рантайм-сеть ошибок (`ScriptComponent`)

`core/components/scripting/scriptc/hotreload.py:99-149` — `_collect_script_errors(path)` возвращает строки `file:line:`:

| Формат | Смысл |
|---|---|
| `path:0: cannot read file: ...` | Файл не читается. |
| `path:lineno:offset: SyntaxError: msg` | Упал `ast.parse`. |
| `path:lineno: No module named 'core.X' (did you mean 'core.maths.math3d' ...)` | Битый `core.*`-импорт; заметьте хинт — модуль математики `core.maths.math3d`, а не `core.math3d`. |
| `path:lineno: cannot import name 'Y' from 'core.X'` | Не тот символ из валидного модуля. |
| `path:lineno: ExcType: msg` | Ошибка выполнения верхнего уровня (строка извлечена из трейсбэка). |

При ошибках класс **не** загружается (`_py_instance = None`, поля сброшены), а сообщение пишется в лог один раз на ревизию файла через `Logger.error`.

Исключения колбэков (`_call_py`, `hotreload.py:253-260`):

```python
try:
    meth(*args)
except Exception as e:
    Logger.error(f"Script {canonical} error '{self.script_path}': {e}")
```

Логируется только `str(e)` — **без трейсбэка и номера строки**. Когда ошибка в Play называет метод, но не строку — воспроизведите под кнопкой Check или оберните подозрительный блок сами (см. ниже). При провале hot-reload дополнительно сохраняется **предыдущая рабочая версия** (`hotreload.py:292-301`): битый релоад откатывает класс/инстанс/методы и ждёт следующего изменения mtime.

## Уровни Logger

`core/foundation/logger.py` — `LogLevel`: `DEBUG = 0`, `INFO = 1`, `WARNING = 2`, `ERROR = 3`:

| Вызов | Поведение |
|---|---|
| `Logger.debug(msg)` / `info` / `warning` | Испускаются, если разрешает `_min_level`; хранятся + рассылаются слушателям (панель консоли редактора — один из них). |
| `Logger.error(msg, exc=None)` | Трейсбэк прикладывается **только** если передан `exc`. |
| `Logger.get_entries()` / `Logger.clear()` / `add_listener(fn)` | Просмотр истории и подписка. |

`Logger` инжектируется в каждый скрипт поведения автоматически.

## Пример диагностики (полный, рабочий)

Скрипт, делающий рантайм-падения находимыми несмотря на `str(e)`-логирование:

```python
import traceback


class Diagnosable:
    verbose: bool = True

    def on_start(self):
        Logger.info(f"Diagnosable on {self._entity.name} (id={self._entity.id})")
        self.body = self._entity.get_component_by_name("Rigidbody")
        if self.body is None and self.verbose:
            Logger.warning("Diagnosable: no Rigidbody; physics branch disabled")

    def on_update(self, dt):
        try:
            self._tick(dt)
        except Exception as e:
            # полный трейсбэк с file:line, т.к. _call_py пишет только str(e):
            Logger.error(f"Diagnosable._tick failed: {traceback.format_exc()}")

    def _tick(self, dt):
        if Input.GetKeyDown(KeyCode.G):
            raise RuntimeError("repro case: press-G handler")
```

Чеклист, когда скрипт молча ничего не делает: сущность `active`? компонент `enabled`? `script_path` резолвится (иначе в инспекторе `Missing:`)? класс детектится (первый в порядке `dir()` класс с lifecycle/collision/gizmo-членом)? ось определена (иначе `GetAxis` — `0.0`)? `play()` вызван с пустым `clip_path`?

## Связанные страницы

- `hot-reload.md` — релоад хранит значения полей и перезапускает `on_awake`/`on_start`.
- `script-component.md` — пути, флаги, резолв классов, Check.
- `pitfalls.md` — полный список граблей с фиксами.

## Проверено по коду

- `editor/panels/script_editor_panel/wid/widget.py:196-197`, `wid/tabs_host.py:135-157` (кнопка Check + отчёт `error [line N]`).
- `core/components/scripting/scriptc/hotreload.py:99-149` (форматы `file:line`), `:151-213` (inspect-путь, скан классов через `dir(mod)`), `:253-260` (логирование только `str(e)`), `:262-301` (проверка mtime, fallback старой версии).
- `core/foundation/logger.py:13-17` (уровни), `:49-68` (`debug/info/warning/error/get_entries/clear`).
