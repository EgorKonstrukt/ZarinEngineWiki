# Команды и Undo

> `CommandHistory(max_size=100)` — стек undo редактора: `execute` / `undo` / `redo` / `seek`, отслеживание dirty-состояния и событие движка `history_changed`. Готовые команды покрывают операции над сущностями.

[Home](../../../index.md) | [Section index](./_section.md)

## Обзор

`Command` — база (`execute`, `undo`, `redo` повторно вызывает `execute`, `merge` возвращает `False`, `description` по умолчанию — имя класса). `CompoundCommand(commands, description)` выполняется по порядку и откатывается в обратном. `CommandHistory(max_size=100)` владеет стеком undo, стеком redo и параллельными снапшотами выделения:

- `execute(command)` выполняет команду, кладёт в стек, чистит redo, обрезает до `max_size` (выкидывая старейшую и сдвигая `_saved_index`) и всегда генерит `history_changed`.
- `undo()` / `redo()` переносят одну команду между стеками, восстанавливают записанное выделение, выставляют `last_affected_entity` (извлекается из `_entity` / `_entity_id` / вложенных `_commands`) и генерят `history_changed` только при зарегистрированных лисенерах и включённой записи.
- `seek(target_head)` прыгает головой undo-стека в абсолютную позицию (откатывает или повторяет N шагов); генерит всегда. Именно его дёргает слайдер панели Undo History.
- Dirty-трекинг: `mark_saved()` запоминает длину стека; `is_dirty` сравнивает длины. `clear()` опустошает оба стека и сбрасывает точку сохранения.
- Лисенеры: `add_on_change(cb)` подписывает колбэк и на undo, и на redo; `set_on_undo(cb)` / `set_on_redo(cb)` управляют каждым списком (`None` чистит). Колбэки получают затронутый `Command`.
- Интроспекция: `can_undo` / `can_redo`, `undo_count` / `redo_count`, `undo_text` (`"Undo <description>"`) / `redo_text`, `get_undo_descriptions()` / `get_redo_descriptions()`, `current_selection`.
- `get_history()` возвращает инжектированный провайдер (`set_history_provider`) или модульный синглтон.

Готовые команды — в `core/foundation/cmd/`: `CreateEntityCommand`, `DeleteEntityCommand`, `InstantiatePrefabCommand` (`entity_cmds.py`), команды добавления/удаления/установки компонентов (`component_cmds.py`).

## Свойства

| имя | тип | дефолт | описание |
|---|---|---|---|
| `CommandHistory(max_size)` | класс | `100` | Стеки undo/redo с лимитом |
| `execute(cmd)` | метод | — | Выполнить, положить, почистить redo, всегда генерит `history_changed` |
| `undo()` / `redo()` | методы | — | Перенести одну команду; генерят только при зарегистрированных лисенерах |
| `seek(target_head)` | метод | — | Прыжок в абсолютную позицию головы; генерит всегда |
| `mark_saved()` / `is_dirty` | метод / prop | — | Точка сохранения vs текущая длина |
| `clear()` | метод | — | Опустошить стеки, сбросить точку сохранения |
| `add_on_change(cb)` | метод | — | Слушать и undo, и redo |
| `set_on_undo(cb)` / `set_on_redo(cb)` | методы | — | Лисенеры по направлениям; `None` чистит |
| `undo_text` / `redo_text` | props | `"Can't ..."` | Метки меню с последним описанием |
| `last_affected_entity` | prop | `None` | Сущность из последней откаченной/повторённой команды |
| `get_history()` | функция | синглтон | Провайдер или модульная глобальная история |

## Использование в редакторе

Панель Undo History (`editor/panels/undo_history_panel.py`) обновляется по таймеру 500 мс при смене ключа `undo_count:redo_count`, показывает описания undo выше и redo ниже разделителя `--- Current State ---`, а по клику вызывает `seek(target)` (в строке лежит `i + 1`). Панель иерархии подписывается через `engine.on("history_changed", ...)` и обновляется при каждом изменении.

## Использование в коде

```python
from core.foundation.cmd.history import get_history
from core.foundation.cmd.entity_cmds import CreateEntityCommand, DeleteEntityCommand

history = get_history()
history.execute(CreateEntityCommand(scene, "Enemy"))
print(history.undo_text)  # "Undo CreateEntityCommand" (или description команды)
history.undo()
history.redo()
history.seek(0)  # назад к старейшему состоянию

history.add_on_change(lambda cmd: print("changed:", cmd.description))
history.mark_saved()
print(history.is_dirty)
```

## Сериализация

Команды не сериализуются; сохраняется только их эффект через обычную сериализацию сцены. `mark_saved`/`is_dirty` — сигнал несохранённых изменений для save-флоу редактора.

## Связанные страницы

- [Foundation: Curve, команды, Time Travel](../foundation.md)
- [Time Travel](./time-travel.md)
- [События движка](./engine-events.md)
- [Сцены (.zpes)](../../scenes-prefabs/scenes.md)
- [Панель профайлера и история Undo](../../editor/profiler-undo.md)

## Проверено по

- `core/foundation/cmd/history.py` (`Command`, `CompoundCommand`, `CommandHistory`, `get_history`, `set_history_provider`, снапшоты выделения, `_emit_history_changed`)
- `core/foundation/cmd/entity_cmds.py` (`CreateEntityCommand`, `DeleteEntityCommand`, `InstantiatePrefabCommand`)
- `core/foundation/cmd/component_cmds.py` (команды компонентов)
- `editor/panels/undo_history_panel.py` (обновление по таймеру, `seek` по клику, строка-разделитель)
- `editor/panels/hierarchy/panel.py` (подписка `engine.on("history_changed", ...)`)
