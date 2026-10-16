# Солвер Culverin

Резервный солвер на чистом Python. Выбирается значением `"solver": "culverin"` в конфиге физики; требуется `pip install culverin`.

## Возможности

| Ключ | Дефолт | Примечания |
|---|---|---|
| `culverin_num_threads` | `0` | `0` форсирует `max_physics_jobs = 0` и `max_physics_barriers = 0` (`solver.py:74-76`) |
| `culverin_max_physics_jobs` | `0` | Передаётся как `max_physics_jobs` в `PhysicsWorld` |
| `culverin_max_physics_barriers` | `0` | Передаётся как `max_physics_barriers` |
| `culverin_max_bodies` | `65536` | Передаётся как `max_bodies` |
| `culverin_max_pairs` | `65536` | Передаётся как `max_pairs` |
| `culverin_max_contact_constraints` | `65536` | Передаётся как `max_contact_constraints` |
| `culverin_temp_allocator_size` | `16777216` | Передаётся как `temp_allocator_size` (16 МиБ) |
| `culverin_penetration_slop` | `0.02` | Передаётся как `penetration_slop` |
| `culverin_enable_ccd` | `False` | Флаг; CCD выключен по умолчанию |
| `culverin_enable_sleeping` | `True` | Флаг; sleeping включён по умолчанию |
| `gravity_x/y/z` | `0.0 / -9.81 / 0.0` | Применяется через `PhysicsWorld.set_gravity` |

## Какие ключи читает/игнорирует

Читает в `initialize` (`solver.py:52-90`): `gravity_x`, `gravity_y`, `gravity_z`, `culverin_num_threads`, `culverin_max_physics_jobs`, `culverin_max_physics_barriers`, `culverin_penetration_slop`, `culverin_max_bodies`, `culverin_max_pairs`, `culverin_max_contact_constraints`, `culverin_temp_allocator_size`, `culverin_enable_ccd`, `culverin_enable_sleeping`.

НЕ читает: `num_sub_steps`, `solver_iterations`, `erp`. `step_simulation(dt)` делает один `world.step(dt)` (`solver.py:133-135`) — цикла подшагов нет.

## Использование в редакторе

Установите `solver` = `culverin` в конфиге проекта. Настраивайте только ключи `culverin_*`; изменение `num_sub_steps` / `solver_iterations` / `erp` на этот бэкенд не влияет.

## Использование в коде

```python
from physics_solvers.culverin_solver.solverp.solver import CulverinSolver

solver = CulverinSolver()
ok = solver.initialize({
    "gravity_y": -9.81,
    "culverin_num_threads": 0,          # 0 => jobs/barriers форсируются в 0
    "culverin_max_bodies": 65536,
    "culverin_max_pairs": 65536,
    "culverin_max_contact_constraints": 65536,
    "culverin_temp_allocator_size": 16777216,
    "culverin_penetration_slop": 0.02,
    "culverin_enable_ccd": False,
    "culverin_enable_sleeping": True,
})
assert ok
solver.step_simulation(0.02)
```

## Заметки fixed update

- `dt` — это `Engine._fixed_dt` (`0.02` по умолчанию); решение выполняется в `solver.step_simulation(dt)`.
- `ray_cast` возвращает `body_id / position / normal=(0,0,0) / fraction`; `get_collision_events` — только пары `body_a / body_b` (без силы).

## Связанные

Solver & Threading, Rigidbody, Collision Layers, PyBullet Solver, PhysX Solver.

## Проверено по

- `physics_solvers/culverin_solver/solverp/solver.py:40-90` (`initialize`, дефолты и имена ключей)
- `config/config.py:352-379` (совпадающие дефолты конфига)
