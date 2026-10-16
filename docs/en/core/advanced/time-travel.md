# Time Travel

> `SnapshotRecorder(max_frames=600, capture_interval=1)` records full scene serializations into a ring buffer and restores any frame via `Scene.deserialize`. It is independent of the command undo stack.

[Home](../../../index.md) | [Section index](./_section.md)

## Overview

`FrameSnapshot` stores one frame: `frame_number`, `timestamp`, `entity_count`, `data` (the full scene dict), `frame_time_ms`, plus `camera_data` (optional), `bookmarked`, `breakpoint_hit`, and an md5 `hash` of the data (`_compute_hash` over sorted-keys JSON). Module helpers diff snapshots: `diff_scenes(old, new)` reports `created` / `deleted` / `changed` entity ids with per-component `(field, old, new)` deltas (skipping `_key`/`type`), and `find_entity_frame_changes(frames, entity_id)` returns frame indices where one entity's data changed.

`SnapshotRecorder(max_frames=600)` keeps a `deque(maxlen=max_frames)`:

- `start()` begins recording (resets counters); `stop()` ends it; `clear()` empties the buffer.
- `capture(scene, frame_time_ms=0.0, camera_data=None)` serializes the scene every `capture_interval`-th call (default `1`, setter clamps to ≥ 1). With `filter_unchanged`, identical hashes are skipped. An optional `breakpoint_expr` is `eval`'d with `scene_data`, `entity_count`, `frame`, `time` (plus `set_breakpoint_ns` extras); a truthy result flags the frame and stops recording.
- `restore(registry, index)` deserializes the snapshot into a new `Scene` and calls `mark_dirty()`; returns `None` on failure (logged).
- Bookmarks: `toggle_bookmark(index)`, `bookmarked_indices()`, `clear_bookmarks()`. `export_frame(index, path)` writes one snapshot as JSON. `frames()` / `get_frame(index)` read the buffer; `num_frames`, `total_frames`, `is_recording`, `max_frames`, `capture_interval` are properties.

Relation to undo: the command stack (`CommandHistory`) records discrete edits and emits `history_changed`; the recorder stores whole-scene states (typically during Play) and the editor swaps scenes on restore, emitting `scene_loaded` — they do not share data.

## Properties

| name | type | default | description |
|---|---|---|---|
| `SnapshotRecorder(max_frames)` | class | `600` | Ring buffer of `FrameSnapshot` |
| `capture_interval` | int | `1` | Record every Nth `capture()` call |
| `filter_unchanged` | bool | `False` | Skip frames whose md5 equals the last |
| `breakpoint_expr` | str | `""` | Pause-recording condition over scene data |
| `capture(scene, ...)` | method | — | Serialize scene; returns buffer index or `None` |
| `restore(registry, index)` | method | — | `Scene.deserialize` + `mark_dirty()` |
| `toggle_bookmark` / `bookmarked_indices` | methods | — | Flag / list bookmarked frames |
| `export_frame(index, path)` | method | `False` on fail | Dump one frame to JSON |
| `diff_scenes` / `find_entity_frame_changes` | functions | — | Snapshot diffing helpers |

## Usage in Editor

The time-travel panel drives `start` / `stop` / `capture` / `restore` / `toggle_bookmark` / `export_frame`; after a restore it emits `scene_loaded` so viewport, hierarchy and inspector rebind (`editor/panels/timetravel/playback.py`).

## Usage in Code

```python
from core.foundation.time_travel import SnapshotRecorder, diff_scenes
from core.ecs import ComponentRegistry

rec = SnapshotRecorder(max_frames=600)
rec.capture_interval = 2
rec.filter_unchanged = True
rec.start()
idx = rec.capture(scene, frame_time_ms=16.6)
rec.stop()

restored = rec.restore(ComponentRegistry, 0)
print(diff_scenes(rec.get_frame(0).data, rec.get_frame(-1).data).keys())
rec.toggle_bookmark(0)
rec.export_frame(0, "frame_0.json")
```

## Serialization

Snapshots are complete scene dicts (same schema as `.zpes`); `export_frame` writes one verbatim as JSON. Restoring re-runs normal `Scene.deserialize`, so unknown component types are skipped exactly like a file load.

## Related pages

- [Foundation: Curve, Commands, Time Travel](../foundation.md)
- [Commands and Undo](./commands-undo.md)
- [Engine Events](./engine-events.md)
- [Scenes (.zpes)](../../scenes-prefabs/scenes.md)

## Verified against

- `core/foundation/time_travel.py` (`FrameSnapshot`, `_compute_hash`, `diff_scenes`, `_diff_entity`, `_diff_component`, `find_entity_frame_changes`, `SnapshotRecorder`)
- `editor/panels/timetravel/playback.py` (`scene_loaded` emit after restore)
- `editor/main_window/handlersp/play.py` (snapshot worker thread)
