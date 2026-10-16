# Commands and Undo

> `CommandHistory(max_size=100)` is the editor undo stack: `execute` / `undo` / `redo` / `seek`, dirty tracking, and the `history_changed` engine event. Ready-made commands cover entity operations.

[Home](../../../index.md) | [Section index](./_section.md)

## Overview

`Command` is the base (`execute`, `undo`, `redo` re-calls `execute`, `merge` returns `False`, `description` defaults to the class name). `CompoundCommand(commands, description)` executes in order and undoes in reverse. `CommandHistory(max_size=100)` owns an undo stack and a redo stack plus parallel selection snapshots:

- `execute(command)` runs the command, pushes it, clears the redo stack, trims to `max_size` (dropping the oldest and shifting `_saved_index`), and always emits `history_changed`.
- `undo()` / `redo()` move one command between stacks, restore the recorded selection, set `last_affected_entity` (extracted from `_entity` / `_entity_id` / nested `_commands`), and emit `history_changed` only when change listeners are registered and recording is on.
- `seek(target_head)` jumps the undo-stack head to an absolute position (undoes or redoes N steps); always emits `history_changed`. This is what the Undo History panel slider drives.
- Dirty tracking: `mark_saved()` records the stack length; `is_dirty` compares lengths. `clear()` empties both stacks and resets the saved index.
- Listeners: `add_on_change(cb)` registers the callback for both undo and redo; `set_on_undo(cb)` / `set_on_redo(cb)` manage each list (`None` clears it). Callbacks receive the affected `Command`.
- Introspection: `can_undo` / `can_redo`, `undo_count` / `redo_count`, `undo_text` (`"Undo <description>"`) / `redo_text`, `get_undo_descriptions()` / `get_redo_descriptions()`, `current_selection`.
- `get_history()` returns the injected provider (`set_history_provider`) or a module-level singleton.

Ready-made commands live in `core/foundation/cmd/`: `CreateEntityCommand`, `DeleteEntityCommand`, `InstantiatePrefabCommand` (`entity_cmds.py`), component add/remove/set commands (`component_cmds.py`).

## Properties

| name | type | default | description |
|---|---|---|---|
| `CommandHistory(max_size)` | class | `100` | Undo/redo stacks with cap |
| `execute(cmd)` | method | — | Run, push, clear redo, always emits `history_changed` |
| `undo()` / `redo()` | methods | — | Move one command; emit only with registered listeners |
| `seek(target_head)` | method | — | Jump to absolute head position; always emits |
| `mark_saved()` / `is_dirty` | method / prop | — | Save-point vs current length |
| `clear()` | method | — | Empty stacks, reset saved index |
| `add_on_change(cb)` | method | — | Listen on both undo and redo |
| `set_on_undo(cb)` / `set_on_redo(cb)` | methods | — | Per-direction listeners; `None` clears |
| `undo_text` / `redo_text` | props | `"Can't ..."` | Menu labels with last description |
| `last_affected_entity` | prop | `None` | Entity extracted from last undone/redone command |
| `get_history()` | function | singleton | Provider or module-global history |

## Usage in Editor

The Undo History panel (`editor/panels/undo_history_panel.py`) refreshes on a 500 ms timer when `undo_count:redo_count` changes, lists undo descriptions above and redo descriptions below a `--- Current State ---` separator, and calls `seek(target)` on click (row payload is `i + 1`). The hierarchy panel subscribes via `engine.on("history_changed", ...)` to refresh on every change.

## Usage in Code

```python
from core.foundation.cmd.history import get_history
from core.foundation.cmd.entity_cmds import CreateEntityCommand, DeleteEntityCommand

history = get_history()
history.execute(CreateEntityCommand(scene, "Enemy"))
print(history.undo_text)  # "Undo CreateEntityCommand" (or the command's description)
history.undo()
history.redo()
history.seek(0)  # back to the oldest state

history.add_on_change(lambda cmd: print("changed:", cmd.description))
history.mark_saved()
print(history.is_dirty)
```

## Serialization

Commands are not serialized; only their effects persist via normal scene serialization. `mark_saved`/`is_dirty` is the unsaved-changes signal for the editor save flow.

## Related pages

- [Foundation: Curve, Commands, Time Travel](../foundation.md)
- [Time Travel](./time-travel.md)
- [Engine Events](./engine-events.md)
- [Scenes (.zpes)](../../scenes-prefabs/scenes.md)
- [Profiler Panel and Undo History](../../editor/profiler-undo.md)

## Verified against

- `core/foundation/cmd/history.py` (`Command`, `CompoundCommand`, `CommandHistory`, `get_history`, `set_history_provider`, selection snapshots, `_emit_history_changed`)
- `core/foundation/cmd/entity_cmds.py` (`CreateEntityCommand`, `DeleteEntityCommand`, `InstantiatePrefabCommand`)
- `core/foundation/cmd/component_cmds.py` (component commands)
- `editor/panels/undo_history_panel.py` (timer refresh, `seek` on click, separator row)
- `editor/panels/hierarchy/panel.py` (`engine.on("history_changed", ...)`)
