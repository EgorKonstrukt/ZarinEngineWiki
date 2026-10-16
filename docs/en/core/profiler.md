# Profiler

> `Profiler` (`core/foundation/profiler.py`) records nested timing sections into flat per-frame data plus an optional rolling buffer of 300 frames. Disabled by default; toggle `profiler.enabled` or `engine.profiler_enabled`.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

All methods are no-ops while `enabled` is `False`. `start(key)` pushes `(key, perf_counter)`; `stop(key)` pops the matching entry (nested-safe: searches the stack when the top mismatches) and accumulates milliseconds into `_flat_data`. `scope(key)` is the context-manager form. `set_value(key, ms)` writes an absolute value (used for `scene_serialize`). `capture_frame()` seals the current frame (frame time, samples with depth + color from `_PROFILER_COLORS`) into a `deque(maxlen=300)`. `Engine` exposes `profiler`, `profiler_data`, `get_profiler_data(key, default)`, `set_profiler_data`, `capture_profiler_frame()`, `reset_profiler()`, and the `profiler_enabled` property. Engine-emitted keys include `tick`, `fixed_update`, `update`, `scene_update`, `scene_fixed_update`, `scene_start`, `scene_serialize`, plus per-plugin `NAME` sections and subsystem keys (`physics_ms`, `render_scene`, `audio_ms`, `ui_ms`, `particle_ms`, `animation_ms`, `gizmo_lines`, `frame_overhead`, …).

## Properties

| name | type | default | description |
|---|---|---|---|
| enabled | bool | `False` | Master switch; toggling clears stack/frame state |
| capture_frames | bool | `False` | When `True`, per-frame samples are retained |
| max_frames | int | `300` | `deque(maxlen=...)` constructor argument |
| start(key) / stop(key) | methods | — | `stop` returns duration ms or `None` |
| scope(key) | context manager | — | `with profiler.scope("my_sys"):` |
| set_value(key, ms) | method | — | Absolute write (not accumulation) |
| capture_frame | method | — | Seal frame into rolling buffer |
| reset | method | — | Clears frames, stack, flat data, counters |
| data | dict[str, float] | `{}` copy | Flat accumulated timings (ms) |
| frames | list[FrameProfile] | copy | Sealed frames with `samples`, `frame_time_ms`, `flat_data` |
| engine surface | properties | — | `engine.profiler`, `engine.profiler_data`, `engine.profiler_enabled` |

## Usage in Editor

Profiler panel reads `engine.profiler_data` at `profiler.refresh_interval` (500 ms default) and the frame buffer for graphs. Enable via global config `profiler.enabled` or at runtime.

## Usage in Code

```python
engine.profiler_enabled = True
with engine.profiler.scope("my_system"):
    step_simulation(dt)
engine.set_profiler_data("my_metric", 1.25)
print(engine.get_profiler_data("tick", 0.0))
engine.capture_profiler_frame()
```

## Serialization

Not applicable. Profiler data is runtime-only.

## Related pages

- [Time and Fixed Update](./time.md)
- [Logging](./logging.md)
- [Configuration](./config.md)

## Verified against

- `core/foundation/profiler.py` (`Profiler`, `ProfileSample`, `FrameProfile`, `_PROFILER_COLORS`, `max_frames=300`)
- `core/engine/eng/loop.py` (`tick_begin`/`tick_fixed_step`/`tick_update` methods emitting `tick`/`fixed_update`/`update` keys, `profiler` property surface)
- `core/config/config.py` (`profiler.enabled`, `profiler.refresh_interval`)
- `core/ecs/query.py` (`scene_update`, `scene_fixed_update`, `scene_start` scopes)
- `core/ecs/scene_serialize.py` (`profiler.set_value("scene_serialize", 0)`)
