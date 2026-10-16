# Панель профайлера и история Undo

> Поверхность производительности (`editor/panels/profiler/`) — `ProfilerPanel` с видами hierarchy/flame/timeline — плюс стек команд: `UndoStack` (`editor/undo_stack.py`), `UndoHistoryPanel`, `TracemallocPanel`.

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| `ProfilerPanel` | `(engine, parent=None)` + `load_config(config)` | `profiler/panel.py`; `showEvent/hideEvent` управляют семплированием |
| Контролы `ProfilerPanel` | `_on_toggle(checked)`, `_on_clear()`, `_refresh()`, `_select_frame(idx)`, `_get_fps()` | Переключатель захвата, очистка, выбор кадра |
| `HierarchyView.set_frame_data` | `(samples: list, frame_time_ms: float, flat_data: dict \| None)` | Дерево агрегированных семплов |
| `FlameGraphWidget.set_data` / `clear_data` | `(samples: list, frame_time_ms: float)` | `profiler/flame.py` |
| `TimelineWidget.add_frame` / `clear_data` | `(flat_data: dict[str, float])` | Прокручиваемый график в `profiler/timeline.py` |
| `TimelineOverview.add_frame` | `(flat_data: dict, frame_time_ms: float)` | Мини-полоса; `_bar_color(total)` масштабирует цвет |
| `ProfilerHeader.update_stats` | `(fps, frame_count, frame_time_ms, ...)` | Шапка-показания в `profiler/views.py` |
| `UndoCommand` | `redo()` / `undo()` (переопределить) | База в `editor/undo_stack.py` |
| `CallbackCommand` | `(do_fn, undo_fn, description="")` | Команда на лямбдах |
| `MacroCommand` | `(commands: list[UndoCommand], description="")` | Атомарная группа |
| `UndoStack` | `push(cmd)`, `undo()`, `redo()`, `can_undo()`, `can_redo()`, `begin_macro(desc)`, `end_macro(desc)`, `clear()`, `undo_description()`, `redo_description()`, `index()` | `QObject` с нотификацией (`_notify`) |
| `UndoHistoryPanel` | `refresh()`, `_on_item_clicked(item)`, `_timed_refresh()` | История с прыжком по клику (`panels/undo_history_panel.py`) |
| `TracemallocPanelBase` | `_build_short_report()`, `_build_full_report()`, `_on_copy_report()`, `_on_save_report()`, `_on_analyze()` | `panels/tracemalloc/panel.py` |
| `TracemallocPanel` | `_refresh()`, `_kick_heavy(do_trace, do_objs)`, `_update_gc_stats()`, `_update_tracemalloc()`, `_format_size(n, show_sign=False)` | `panels/tracemalloc/__init__.py`; тяжёлая работа вне UI-потока |

## Usage in Editor

Док профайлера: переключатель захвата, выбор кадра для заморозки hierarchy/flame/timeline, Clear сбрасывает кадры; шапка показывает FPS/число кадров/время кадра. История Undo lists pushed-команды с описаниями — клик по записи откатывает/повторяет до неё. Док tracemalloc строит краткие/полные отчёты аллокаций, копируемые в буфер или сохраняемые на диск, рядом — статистика GC. Глобальные действия Undo/Redo живут в меню Edit главного окна.

## Usage in Code

```python
from editor.undo_stack import UndoStack, CallbackCommand, MacroCommand

stack = UndoStack()
stack.push(CallbackCommand(do_fn=apply, undo_fn=revert, description="Move entity"))
stack.begin_macro("Drop asset")
# ... несколько push ...
stack.end_macro("Drop asset")
stack.undo(); stack.redo()
```

## Related

- Главное окно (действия Edit → Undo/Redo), Иерархия/Инспектор (производители команд), Консоль (поверхность ошибок)
- `tools/profile_flamegraph.bat`, `tools/profile_svg.bat`, `tools/profile_top.bat` (офлайн-профилирование)

## Verified against

- `editor/panels/profiler/panel.py`, `editor/panels/profiler/views.py`, `editor/panels/profiler/flame.py`, `editor/panels/profiler/timeline.py`
- `editor/undo_stack.py`, `editor/panels/undo_history_panel.py`
- `editor/panels/tracemalloc/panel.py`, `editor/panels/tracemalloc/__init__.py`
