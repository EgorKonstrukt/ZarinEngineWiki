# Солвер и многопоточность

Где реально работает симуляция и как данные пересекают границу потоков/процессов, не блокируя геймплейный код.

## Свойства

| Настройка | По умолчанию | Где |
|---|---|---|
| `simulation_mode` | `multi_threaded` | `single` / `multi_threaded` / `per_layer_process` |
| `solver` | `culverin` | `culverin` (Jolt) / `pybullet` / `physx` |
| `fixed_time_step` | `0.02` | Шаг `dt` |
| `num_sub_steps` | `2` | Конфиг (собственный дефолт PyBullet-солвера `1`; Culverin/Jolt игнорирует) |
| `solver_iterations` | `10` | Конфиг (собственный дефолт PyBullet `50`; Culverin/Jolt игнорирует) |
| `erp` / `contact_erp` / `friction_erp` | `0.2` / `0.2` / `0.0` | Конфиг (собственные дефолты PyBullet `erp`/`contact_erp` — `0.4`; Culverin/Jolt игнорирует) |
| `culverin_max_bodies` / `max_pairs` / `max_contact_constraints` | `65536` | Капы Jolt-мира |
| `culverin_temp_allocator_size` | `16777216` | 16 МБ |
| `culverin_num_threads` / `max_jobs` / `max_barriers` | `0` / `0` / `0` | `0` = однопоточный Jolt |
| `culverin_penetration_slop` | `0.02` | |
| `culverin_enable_ccd` / `enable_sleeping` | `False` / `True` | |
| Флаги shared-памяти | `1` active, `2` dirty, `4` kinematic/teleport, `8` 2D | `SharedPhysicsBuffer` |
| Лимиты | `65536` сущностей, `16` soft-тел × `32768` вершин | Буферы |

## Использование в редакторе

Прототип — дефолтный `multi_threaded`. Для `CharacterController`/`Motor` и отладки — `single`; для изолированных арен — `per_layer_process`.

## Использование в коде

```python
# Рантайм API солвера (IPhysicsSolver):
solver.set_gravity((0.0, -9.81, 0.0))
solver.step_simulation(0.02)
solver.apply_force(body_id, (0.0, 10.0, 0.0))
solver.apply_torque(body_id, (0.0, 1.0, 0.0))
solver.apply_impulse(body_id, (0.0, 5.0, 0.0))
hit = solver.ray_cast((0.0, 5.0, 0.0), (0.0, -1.0, 0.0), 100.0)
```

Порядок фонового шага: склейка `step`-команд в один `dt` → телепорты/силы из shared-памяти → `step_simulation(dt)` → обнуление внеплоскостных скоростей 2D → запись назад с bump версии (+ soft-буферы) → маппинг id тел в id сущностей для событий. In-process путь зеркалит без сериализации: регистрация → проверка форм (каждые 60 шагов) → ECS→физика → разделение soft-тел (только при `≥2`) → шаг → 2D-ограничение → физика→ECS → soft-синхронизация → события.

## Заметки о fixed update

- `Engine.tick_fixed_step`: накопление по `_fixed_dt` (`0.02`), потолок `5 * _fixed_dt`, макс. `5` шагов/тик, затем `scene.fixed_update(fd)` (компонентный `on_fixed_update`) и наконец `step(fd)` системных плагинов.
- `PhysicsWorker`: демон `threading.Thread`, опрос очереди `0.004` с. `SceneThreadPool`: `max_workers = 2`, `ThreadPoolExecutor` `phys-scene`.
- ECS→физика — только по грязному трансформу или накопленной силе; физика→ECS пропускает кинематику/грязные; аккумуляторы сил очищаются каждый пуш — `add_force` вызывать каждый шаг.

## Связанные

Обзор физики, Rigidbody, слои, CharacterController, Motor.

## Проверено по

- `core/physics/proc/process.py`, `proc/sync.py`
- `core/physics/physics_worker.py`, `scene/scene.py`, `scene/threadpool.py`
- `core/physics/shared_buffer.py`, `soft_shared_buffer.py`
- `core/config/config.py`, `core/engine/eng/loop.py`, `plugins/physics_plug/plugin.py`
- `physics_solvers/registry.py`, `culverin_solver/solverp/solver.py`
