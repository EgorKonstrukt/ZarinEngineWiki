# Foundation: Curve, Commands, Time Travel

> `core/foundation/` holds engine-wide utilities: `Curve` (keyframed float function), the `Command`/`CommandHistory` undo stack, and `SnapshotRecorder` (frame-by-frame scene snapshots for time travel).

[Home](../../index.md) | [Section index](./_section.md)

## Overview

**Curve** (`core/foundation/curve.py`): `@dataclass Curve(keys, pre_wrap="clamp", post_wrap="clamp")`. `CurveKey(time, value, in_tangent=0.0, out_tangent=0.0, tangent_mode=SMOOTH)`. `TangentMode`: `FREE`, `LINEAR`, `CONSTANT`, `SMOOTH`. `add_key` inserts sorted and re-smooths SMOOTH tangents; `evaluate(t)` clamps outside the key range, returns `CONSTANT`/`LINEAR`/Hermite inside. `evaluate_array` uses the Cython batch path when available. `to_list`/`from_list` store `[[time, value]]`; `to_dict`/`from_dict` keep tangents and wrap modes. `QuaternionCurve` is the slerp-interpolated rotation variant (`(0,0,0,1)` when empty).

**Commands** (`core/foundation/cmd/`): `Command` base (`execute`, `undo`, `redo` → re-`execute`, `merge` → `False`, `description`). `CompoundCommand(commands, description)` runs/reverts in order/reverse. `CommandHistory(max_size=100)`: `execute` (clears redo), `undo`/`redo`, `seek(target_head)` for the history panel slider, `mark_saved`/`is_dirty`, `can_undo`/`can_redo`, `undo_text`/`redo_text`, `undo_count`/`redo_count`, description lists. `get_history()` returns the provider or a module singleton. Ready-made: `CreateEntityCommand`, `DeleteEntityCommand`, `InstantiatePrefabCommand`. `execute` and `seek` always emit the `history_changed` engine event; `undo`/`redo` emit it when change listeners are registered (`add_on_change` / `set_on_undo` / `set_on_redo`).

**Time travel** (`core/foundation/time_travel.py`): `SnapshotRecorder(max_frames=600)` keeps `FrameSnapshot(frame_number, timestamp, entity_count, data, frame_time_ms)` with md5 `hash`, optional `camera_data`, `bookmarked`, `breakpoint_hit`. `capture_interval` (default `1`), `filter_unchanged`, `breakpoint_expr` (evaluated with `scene_data`, `entity_count`, `frame`, `time`; a truthy result stops recording). `capture(scene)` serializes the scene; `restore(registry, index)` deserializes a snapshot back into a `Scene`. Helpers: `diff_scenes`, `find_entity_frame_changes`, `toggle_bookmark`, `export_frame`.

## Properties

| name | type | default | description |
|---|---|---|---|
| Curve.add_key(time, value) | method | — | Sorted insert + auto-smooth; returns `CurveKey` |
| Curve.evaluate(t) | method | `0.0` empty | Clamped Hermite/LINEAR/CONSTANT interpolation |
| TangentMode | Enum | `SMOOTH` | `free/linear/constant/smooth` |
| CommandHistory | class | `max_size=100` | Undo/redo/seek/dirty tracking |
| history_changed | event | — | Emitted on `execute`/`seek` always; on `undo`/`redo` when change listeners are registered |
| SnapshotRecorder | class | `max_frames=600` | Ring buffer of scene snapshots |
| SnapshotRecorder.capture_interval | int | `1` | Record every Nth frame |

## Usage in Editor

Curve editor panel edits `Curve` fields (inspector `curve` type). Undo history panel drives `seek()`. Time-travel panel drives `start/stop/capture/restore/toggle_bookmark/export_frame`.

## Usage in Code

```python
from core.foundation.curve import Curve
from core.foundation.cmd.history import get_history
from core.foundation.cmd.entity_cmds import CreateEntityCommand
from core.foundation.time_travel import SnapshotRecorder
from core.ecs import ComponentRegistry

curve = Curve()
curve.add_key(0.0, 0.0)
curve.add_key(1.0, 10.0)
print(curve.evaluate(0.5))

history = get_history()
history.execute(CreateEntityCommand(scene, "Enemy"))
history.undo()

rec = SnapshotRecorder()
rec.start()
rec.capture(scene)
restored = rec.restore(ComponentRegistry, 0)
```

## Serialization

Curves serialize as `to_dict()` (`keys` with `time/value/in_tangent/out_tangent/tangent_mode`, `pre_wrap`, `post_wrap`); `ScriptComponent` stores them under `script_fields`. Snapshots serialize as full scene dicts (`export_frame` writes one to JSON).

## Related pages

- [ECS Overview](./ecs-overview.md)
- [Components and Lifecycle](./components-lifecycle.md)
- [Registry and Serialization](./registry-serialization.md)
- [Time and Fixed Update](./time.md)

## Verified against

- `core/foundation/curve.py` (`Curve`, `CurveKey`, `TangentMode`, `QuaternionCurve`)
- `core/foundation/cmd/history.py` (`Command`, `CompoundCommand`, `CommandHistory`, `get_history`, `history_changed`)
- `core/foundation/cmd/entity_cmds.py` (`CreateEntityCommand`, `DeleteEntityCommand`, `InstantiatePrefabCommand`)
- `core/foundation/time_travel.py` (`SnapshotRecorder`, `FrameSnapshot`, `diff_scenes`, `restore`)
- `core/components/scripting/scriptc/component.py` (Curve in `script_fields`)
