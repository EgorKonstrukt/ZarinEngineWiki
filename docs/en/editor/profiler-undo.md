# Profiler Panel and Undo History

> Performance surface (`editor/panels/profiler/`) — `ProfilerPanel` with hierarchy/flame/timeline views — plus the command stack: `UndoStack` (`editor/undo_stack.py`), `UndoHistoryPanel`, `TracemallocPanel`.

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `ProfilerPanel` | `(engine, parent=None)` + `load_config(config)` | `profiler/panel.py`; `showEvent/hideEvent` gate sampling |
| `ProfilerPanel` controls | `_on_toggle(checked)`, `_on_clear()`, `_refresh()`, `_select_frame(idx)`, `_get_fps()` | Capture toggle, clear, frame pick |
| `HierarchyView.set_frame_data` | `(samples: list, frame_time_ms: float, flat_data: dict \| None)` | Tree of aggregated samples |
| `FlameGraphWidget.set_data` / `clear_data` | `(samples: list, frame_time_ms: float)` | `profiler/flame.py` |
| `TimelineWidget.add_frame` / `clear_data` | `(flat_data: dict[str, float])` | `profiler/timeline.py` scrolling chart |
| `TimelineOverview.add_frame` | `(flat_data: dict, frame_time_ms: float)` | Mini-map strip; `_bar_color(total)` scales color |
| `ProfilerHeader.update_stats` | `(fps, frame_count, frame_time_ms, ...)` | `profiler/views.py` header readout |
| `UndoCommand` | `redo()` / `undo()` (override) | Base in `editor/undo_stack.py` |
| `CallbackCommand` | `(do_fn, undo_fn, description="")` | Lambda-backed command |
| `MacroCommand` | `(commands: list[UndoCommand], description="")` | Atomic group |
| `UndoStack` | `push(cmd)`, `undo()`, `redo()`, `can_undo()`, `can_redo()`, `begin_macro(desc)`, `end_macro(desc)`, `clear()`, `undo_description()`, `redo_description()`, `index()` | `QObject` with change notify (`_notify`) |
| `UndoHistoryPanel` | `refresh()`, `_on_item_clicked(item)`, `_timed_refresh()` | Click-to-jump history (`panels/undo_history_panel.py`) |
| `TracemallocPanelBase` | `_build_short_report()`, `_build_full_report()`, `_on_copy_report()`, `_on_save_report()`, `_on_analyze()` | `panels/tracemalloc/panel.py` |
| `TracemallocPanel` | `_refresh()`, `_kick_heavy(do_trace, do_objs)`, `_update_gc_stats()`, `_update_tracemalloc()`, `_format_size(n, show_sign=False)` | `panels/tracemalloc/__init__.py`; heavy work off-thread |

## Usage in Editor

Profiler dock: toggle capture, pick a frame to freeze hierarchy/flame/timeline on it, Clear drops all frames; the header shows FPS/frame-count/frame-time. Undo History lists pushed commands with descriptions — clicking an entry undoes/redoes to that point. Tracemalloc dock builds short/full allocation reports, copyable to clipboard or savable to disk, with GC stats alongside. Global Undo/Redo menu actions live in the main window Edit menu.

## Usage in Code

```python
from editor.undo_stack import UndoStack, CallbackCommand, MacroCommand

stack = UndoStack()
stack.push(CallbackCommand(do_fn=apply, undo_fn=revert, description="Move entity"))
stack.begin_macro("Drop asset")
# ... push several commands ...
stack.end_macro("Drop asset")
stack.undo(); stack.redo()
```

## Related

- Main Window (Edit → Undo/Redo actions), Hierarchy/Inspector (command producers), Console (error surface)
- `tools/profile_flamegraph.bat`, `tools/profile_svg.bat`, `tools/profile_top.bat` (offline profiling)

## Verified against

- `editor/panels/profiler/panel.py`, `editor/panels/profiler/views.py`, `editor/panels/profiler/flame.py`, `editor/panels/profiler/timeline.py`
- `editor/undo_stack.py`, `editor/panels/undo_history_panel.py`
- `editor/panels/tracemalloc/panel.py`, `editor/panels/tracemalloc/__init__.py`
