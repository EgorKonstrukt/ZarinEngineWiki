# Многопоточность

> Работа Play-режима разнесена по потокам: `GameWorker` тикает движок (QThread, 120 Гц update / 60 Гц fixed из конфига), `PhysicsWorker` шагает солвер в daemon-потоке (опрос команд каждые 4 мс), а `SceneThreadPool(2)` крутит фоновые задачи сцены. Координируются через неблокирующие локи, очереди команд и `SharedPhysicsBuffer` — но никогда через вызовы Qt/GL.

[Home](../../../index.md) | [Section index](./_section.md)

## Обзор

- `GameWorker(QThread)` (`core/engine/engine_worker.py`): создаётся в `Engine.start_play` с `update_rate = конфиг rendering.tick_rate (120.0)` и `fixed_rate = конфиг rendering.fixed_tick_rate (60.0)`. Каждый виток: неблокирующий захват `engine._scene_lock` → `tick_begin`; до `_MAX_FIXED_STEPS = 5` × `tick_fixed_step`; затем `tick_update`. Отставание больше 10 периодов update сбрасывает часы (пропуск кадров). `stop(timeout_ms=2000)` ставит event и ждёт. Поэтапные локи дают рендеру (main-поток) забирать `engine._scene_lock` между этапами.
- `PhysicsWorker(threading.Thread, daemon=True)` (`core/physics/physics_worker.py`): очередь команд на вход, очередь результатов на выход; цикл опрашивает с `timeout=0.004` (4 мс). Команды: `init`, `load_bodies`, `step` (кинематика внутрь → `step_simulation(dt)` → трансформы/коллизии наружу), `add_body`, `remove_bodies`, `unload_all`, `shutdown`. Солвер и `PhysicsScene` живут только в этом потоке. Хелперы: `send`, `poll`, `drain_results`, `wait_for_result(expected_type, timeout=5.0)`, `shutdown(timeout=3.0)`.
- `SceneThreadPool(max_workers=2)` (`core/physics/scene/threadpool.py`): `ThreadPoolExecutor(thread_name_prefix="phys-scene")` со `start` / `stop(wait)` / `submit` / `flush` (барьер 5 с) / `running`. `ThreadpoolMixin` добавляет `start_background_workers(max_workers=2)`, `stop_background_workers`, `submit_background`.
- `SharedPhysicsBuffer` (`core/physics/shared_buffer.py`, `multiprocessing.shared_memory`, `MAX_ENTITIES = 65536`): заголовок `result_version` (uint64) + `num_entities` (int32), затем послотовые массивы флагов/body-map/entity-data/force-data/result-data (пейлоад float32). Биты флагов: bit0 active (`set_active`), bit1 dirty (`set_dirty`), bit2 kinematic (`set_kinematic`), bit3 2D (`set_2d`). Эйлеровы вращения конвертируются в кватернионы при записи.
- Другие daemon-потоки в кодовой базе (тот же паттерн — очереди, без вызовов UI): навигационный воркер `zarin-navmesh`, `scene-save-worker` / `scene-load-worker`, bring-up процесса физики, `player-physics-load`.

Правила для воркер-кода (как это делает сам код): воркеры трогают только данные движка под `engine._scene_lock` (неблокирующий `acquire`), состояние солвера, очереди и shared-буферы. Виджеты Qt и вызовы GL остаются в main-потоке — `GameWorker` их никогда не вызывает; хуки вьюпорта/плагинового UI (`on_viewport_ready`, доки) выполняются в main-потоке.

## Свойства

| имя | тип | дефолт | описание |
|---|---|---|---|
| `GameWorker(engine, update_rate, fixed_rate)` | QThread | `120.0` / `60.0` | Цикл тиков Play; поэтапные неблокирующие локи |
| `_MAX_FIXED_STEPS` | int | `5` | Лимит fixed-шагов за виток |
| `GameWorker.stop(timeout_ms)` | метод | `2000` | Поставить stop-event, ждать |
| таймаут опроса `PhysicsWorker` | float | `0.004` с | Каденс опроса очереди команд |
| `PhysicsWorker.wait_for_result` | метод | `5.0` с | Ожидание типизированного результата |
| `SceneThreadPool(max_workers)` | класс | `2` | Экзекутор `phys-scene` + барьер flush |
| `SharedPhysicsBuffer.MAX_ENTITIES` | int | `65536` | Число слотов всех поэлементных массивов |
| `rendering.tick_rate` / `rendering.fixed_tick_rate` | конфиг | `120.0` / `60.0` | Источник частот `GameWorker` |

## Использование в редакторе

Открытие/сохранение сцен идут в daemon-потоках `scene-open-worker` / `scene-save-worker` с колбэками прогресса; воркер снапшотов time-travel — тоже daemon-поток. Панели редактора не должны трогать данные сцены вне main-потока без лока сцены.

## Использование в коде

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

## Сериализация

Потоки и хэндлы shared-памяти никогда не сериализуются; в dict сцен/компонентов текут только их пейлоады (трансформы, силы).

## Связанные страницы

- [Время и Fixed Update](../time.md)
- [Солвер и многопоточность](../../physics-3d/solver-threading.md)
- [Точность и 64 бита](./precision-64bit.md)
- [События движка](./engine-events.md)

## Проверено по

- `core/engine/engine_worker.py` (`GameWorker`, частоты, `_MAX_FIXED_STEPS`, поэтапные локи, `stop`)
- `core/engine/eng/engine.py` (`start_play`/`stop_play`, конфиг `rendering.tick_rate` / `rendering.fixed_tick_rate`)
- `core/physics/physics_worker.py` (`PhysicsWorker`, опрос `0.004`, набор команд, хелперы очередей)
- `core/physics/scene/threadpool.py` (`SceneThreadPool`, `ThreadpoolMixin`)
- `core/physics/shared_buffer.py` (`MAX_ENTITIES`, оффсеты, биты флагов, API чтения/записи)
- `core/navigation/worker.py` (daemon-паттерн `zarin-navmesh`)
- `core/engine/eng/scenes.py` (daemon-воркеры save/load)
