# Time and Fixed Update

> Frame time flows through `Engine.tick()`: accumulator + fixed steps (`fixed_dt`, default `0.02`, max 5 steps) then one variable-`dt` update scaled by `time_scale`.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

`Engine.tick()` is a no-op unless `play_mode` is `True`. `tick_begin()` flushes dirty transforms, measures raw `dt` with `perf_counter`, multiplies by `time_scale`, and accumulates into `_fixed_accum` (clamped to `5 * fixed_dt`). Then up to `MAX_FIXED_STEPS = 5` calls to `tick_fixed_step()` run `Scene.fixed_update(fd)` plus system-plugin `pre_step`/`step`, each consuming one `fixed_dt`. `tick_update(dt)` runs `Scene.update(dt)` once and updates FPS/TPS counters from unscaled time (0.5 s window). `Engine.initialize()` reads `engine.time_scale` (default `1.0`) and `engine.fixed_update_dt` (default `0.02`, minimum `0.001`) from the global config.

## Properties

| name | type | default | description |
|---|---|---|---|
| time_scale | float | `1.0` | Setter clamps to `>= 0.0`; scales `dt` |
| fixed_dt | float | `0.02` | Setter clamps to `>= 0.001` |
| MAX_FIXED_STEPS | int | `5` | Cap per `tick()` (local in `loop.py`, mirrored in `core/config/constants.py`) |
| fps / tps | float | `0.0` | Measured counters, 0.5 s window |
| frame_count | int | `0` | Incremented per `tick_update` |
| tick() | method | — | No-op when not in play mode |
| on(event, cb) / off(event, cb) | methods | — | Event bus (`EngineEventsMixin`) |

## Usage in Editor

Play button → `start_play()` (resets accumulator, `Scene.start()`, starts `GameWorker` with `rendering.tick_rate` 120 Hz / `rendering.fixed_tick_rate` 60 Hz). Stop → `stop_play()` (stops worker and all audio). `time_scale` is editable in settings; `0` pauses logic time while frames continue.

## Usage in Code

```python
engine.time_scale = 0.5   # slow motion: dt halved
engine.fixed_dt = 1.0 / 60.0

def on_play(data):
    print("play started")

engine.on("play_start", on_play)
engine.off("play_start", on_play)
```

```python
class PhysicsReader:
    def on_fixed_update(self, dt: float):
        assert dt == engine.fixed_dt  # constant step
```

## Serialization

Not applicable. `time_scale` / `fixed_update_dt` persist in `~/.zarin/settings.json` via `Config`.

## Related pages

- [ECS Overview](./ecs-overview.md)
- [Components and Lifecycle](./components-lifecycle.md)
- [Configuration](./config.md)
- [Profiler](./profiler.md)

## Verified against

- `core/engine/eng/engine.py` (`__init__` defaults, `initialize`, `start_play`, `stop_play`, `time_scale`/`fixed_dt` in loop mixin)
- `core/engine/eng/loop.py` (`tick`, `tick_begin`, `tick_fixed_step`, `tick_update`, FPS/TPS)
- `core/engine/eng/events.py` (`on`, `off`, `_emit_event`)
- `core/config/constants.py` (`MAX_FIXED_STEPS = 5`)
- `core/config/config.py` (`engine.time_scale`, `engine.fixed_update_dt`, `rendering.tick_rate`, `rendering.fixed_tick_rate`)
