# Солвер PhysX

USD/Omni-бэкенд (`ovphysx`). Тензорный: позы, скорости и силы идут через связанные таблицы; гравитация запекается в генерируемую сцену.

## Свойства

| Ключ | Дефолт | Примечания |
|---|---|---|
| `physx_device` / `device` | `"cpu"` | `opts.get("physx_device", opts.get("device", "cpu"))`; `PhysX(device=device)` |
| `gravity_x/y/z` | `0.0 / -9.81 / 0.0` | Хранится; пишется в USD как `physics:gravityDirection` + `physics:gravityMagnitude` (`scenes.py`) |
| `debug_draw` | сеттер — no-op (`pass`) | Чтение работает, включение ничего не делает на этом бэкенде |

## Что читает `initialize`

Только `physx_device`/`device` и `gravity_x/y/z` (`solver.py:57-71`). Честные ограничения, проверены grep по всему пакету `physx_solver/`:

- `num_sub_steps` — **не читает** (совпадений в пакете нет).
- `solver_iterations` — **не читает** (совпадений в пакете нет).
- `erp` — **не читает** (совпадений в пакете нет).

`set_gravity` только хранит кортеж и ставит `_bodies_dirty = True`; эффект — при (пере)построении сцены. `step_simulation(dt)` копит `_sim_time += dt`, вызывает `ov_physx.step(dt, sim_time)` + `wait_all()`, затем тянет трансформы из pose-таблицы.

## Использование в коде

```python
from physics_solvers.physx_solver.solverp.solver import PhysXSolver

solver = PhysXSolver()
ok = solver.initialize({
    "physx_device": "cpu",   # или "cuda"/"gpu" в зависимости от сборки ovphysx
    "gravity_x": 0.0,
    "gravity_y": -9.81,
    "gravity_z": 0.0,
})
assert ok
solver.step_simulation(0.02)
```

Рейкаст возвращает ближайшее тело по дистанции до `hit["position"]`: `body_id / position / normal / distance`. `get_collision_events` адаптирует `get_contact_report()` (headers/points) в `body_a / body_b / type / points[position, normal, impulse, separation]`.

## Заметки fixed update

- `dt` — это `Engine._fixed_dt` (`0.02` по умолчанию); шаг выполняется в `solver.step_simulation(dt)`.
- Отложенные записи скоростей/сил сбрасываются перед каждым шагом (`_flush_pending`).

## Связанные

Culverin Solver, PyBullet Solver, Solver & Threading, Rigidbody, Collision Layers.

## Проверено по

- `physics_solvers/physx_solver/solverp/solver.py:17-78` (дефолты `__init__`, чтения ключей `initialize`)
- `physics_solvers/physx_solver/solverp/solver.py:183-196` (`step_simulation`)
- `physics_solvers/physx_solver/solverp/scenes.py:54-81` (экспорт гравитации direction/magnitude в USD)
- `physics_solvers/physx_solver/solverp/solver.py:272-311` (мэппинг `get_collision_events`)
