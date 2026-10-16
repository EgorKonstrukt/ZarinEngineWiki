# Threading

> Play-mode work is split across threads: `GameWorker` ticks the engine (QThread, 120 Hz update / 60 Hz fixed from config), `PhysicsWorker` steps the solver on a daemon thread (4 ms command poll), and `SceneThreadPool(2)` runs background scene jobs. They coordinate through non-blocking locks, command queues and `SharedPhysicsBuffer` — never through Qt/GL calls.

[Home](../../../index.md) | [Section index](./_section.md)

## Overview

- `GameWorker(QThread)` (`core/engine/engine_worker.py`): constructed in `Engine.start_play` with `update_rate = config rendering.tick_rate (120.0)` and `fixed_rate = config rendering.fixed_tick_rate (60.0)`. Each loop: non-blocking `engine._scene_lock` acquire → `tick_begin`; up to `_MAX_FIXED_STEPS = 5` × `tick_fixed_step`; then `tick_update`. Falling more than 10 update periods behind resets the clock (frame skip). `stop(timeout_ms=2000)` sets the event and waits. The staged locking lets the renderer (main thread) grab `engine._scene_lock` between stages.
- `PhysicsWorker(threading.Thread, daemon=True)` (`core/physics/physics_worker.py`): command queue in, result queue out; the loop polls with `timeout=0.004` (4 ms). Commands: `init`, `load_bodies`, `step` (kinematic sync in → `step_simulation(dt)` → transforms/collisions out), `add_body`, `remove_bodies`, `unload_all`, `shutdown`. Solver and `PhysicsScene` live only on this thread. Helpers: `send`, `poll`, `drain_results`, `wait_for_result(expected_type, timeout=5.0)`, `shutdown(timeout=3.0)`.
- `SceneThreadPool(max_workers=2)` (`core/physics/scene/threadpool.py`): `ThreadPoolExecutor(thread_name_prefix="phys-scene")` with `start` / `stop(wait)` / `submit` / `flush` (5 s barrier) / `running`. `ThreadpoolMixin` adds `start_background_workers(max_workers=2)`, `stop_background_workers`, `submit_background`.
- `SharedPhysicsBuffer` (`core/physics/shared_buffer.py`, `multiprocessing.shared_memory`, `MAX_ENTITIES = 65536`): header `result_version` (uint64) + `num_entities` (int32), then per-slot flags/body-map/entity-data/force-data/result-data arrays (float32 payload). Flag bits: bit0 active (`set_active`), bit1 dirty (`set_dirty`), bit2 kinematic (`set_kinematic`), bit3 2D (`set_2d`). Euler rotations are converted to quaternions on write.
- Other daemon threads in the codebase (same pattern — queues, no UI calls): `zarin-navmesh` navigation worker, `scene-save-worker` / `scene-load-worker`, `physics` process bring-up, `player-physics-load`.

Rules for worker code (as the code practices it): workers touch only engine data behind `engine._scene_lock` (non-blocking `acquire`), solver state, queues and shared buffers. Qt widgets and GL calls stay on the main thread — `GameWorker` never calls them; viewport/plugin UI hooks (`on_viewport_ready`, docks) run on the main thread.

## Properties

| name | type | default | description |
|---|---|---|---|
| `GameWorker(engine, update_rate, fixed_rate)` | QThread | `120.0` / `60.0` | Play tick loop; staged non-blocking locking |
| `_MAX_FIXED_STEPS` | int | `5` | Fixed-step cap per loop iteration |
| `GameWorker.stop(timeout_ms)` | method | `2000` | Set stop event, wait |
| `PhysicsWorker` poll timeout | float | `0.004` s | Command-queue poll cadence |
| `PhysicsWorker.wait_for_result` | method | `5.0` s | Typed result wait |
| `SceneThreadPool(max_workers)` | class | `2` | `phys-scene` executor + flush barrier |
| `SharedPhysicsBuffer.MAX_ENTITIES` | int | `65536` | Slot count for all per-entity arrays |
| `rendering.tick_rate` / `rendering.fixed_tick_rate` | config | `120.0` / `60.0` | `GameWorker` rates source |

## Usage in Editor

Scene open/save run on `scene-open-worker` / `scene-save-worker` daemon threads with progress callbacks; the time-travel snapshot worker is a daemon thread too. Editor panels must not touch scene data off the main thread without the scene lock.

## Usage in Code

```python
from core.engine.engine_worker import GameWorker
from core.physics.physics_worker import PhysicsWorker
from core.physics.scene.threadpool import SceneThreadPool
from core.physics.shared_buffer import SharedPhysicsBuffer

worker = GameWorker(engine, update_rate=120.0, fixed_rate=60.0)
worker.start()
# ... play ...
worker.stop()

phys = PhysicsWorker()
phys.start()
phys.send({"type": "step", "dt": 1.0 / 60.0, "ecs_data": {}, "need_collisions": True})
result = phys.wait_for_result("step_result", timeout=5.0)

pool = SceneThreadPool(max_workers=2)
pool.start()
fut = pool.submit(expensive_job, arg)
pool.flush()

shm = SharedPhysicsBuffer().create()
shm.set_num_entities(1)
shm.set_active(0, True)
shm.write_entity_data(0, (0, 0, 0), (0, 0, 0, 1), (0, 0, 0), (0, 0, 0))
```

## Serialization

Threads and shared-memory handles are never serialized; only their payloads (transforms, forces) flow into scene/component dicts.

## Related pages

- [Time and Fixed Update](../time.md)
- [Solver and Threading](../../physics-3d/solver-threading.md)
- [Precision and 64-bit](./precision-64bit.md)
- [Engine Events](./engine-events.md)

## Verified against

- `core/engine/engine_worker.py` (`GameWorker`, rates, `_MAX_FIXED_STEPS`, staged locking, `stop`)
- `core/engine/eng/engine.py` (`start_play`/`stop_play`, config `rendering.tick_rate` / `rendering.fixed_tick_rate`)
- `core/physics/physics_worker.py` (`PhysicsWorker`, `0.004` poll, command set, queue helpers)
- `core/physics/scene/threadpool.py` (`SceneThreadPool`, `ThreadpoolMixin`)
- `core/physics/shared_buffer.py` (`MAX_ENTITIES`, offsets, flag bits, read/write API)
- `core/navigation/worker.py` (`zarin-navmesh` daemon pattern)
- `core/engine/eng/scenes.py` (daemon save/load workers)
