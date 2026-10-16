# Солвер PyBullet

Бэкенд на Bullet: фильтрация коллизий по слоям, составные тела и ERP-мэппинг пружинных джойнтов.

## Свойства

| Поле | Дефолт кода (`solver.py:16-35`) | Дефолт конфига (`config.py:359-362`) | Примечания |
|---|---|---|---|
| `fixed_time_step` | `1/60` | `0.02` | Клэмп `max(0.001, …)`; на шаг `internal_dt = dt / sub_steps` |
| `num_sub_steps` | `1` | `2` | Цикл в `step_simulation`: `numSubSteps=1` + N вызовов `stepSimulation` |
| `solver_iterations` | `50` | `10` | Пробрасывается как `numSolverIterations` |
| `erp` | `0.4` | `0.2` | Пробрасывается как `erp` |
| `contact_erp` | `0.4` | `0.2` | Пробрасывается как `defaultContactERP` |
| `friction_erp` | `0.0` | `0.0` | Пробрасывается как `frictionERP` |
| `contact_breaking_threshold` | `0.02` | `0.02` | Совпадает |
| `linear_damping` / `angular_damping` | `0.04` / `0.04` | `0.04` / `0.04` | Хранимые дефолты |
| `enable_sleeping` / `headless` | `True` / `True` | — | `p.DIRECT` при headless, иначе `p.GUI` |

## Использование в редакторе

Установите `solver` = `pybullet`. Слои коллизий работают через `setCollisionFilterPair`: пара сталкивается только если `(groupA & maskB) != 0 and (groupB & maskA) != 0` (`solver.py:281-294`).

## Использование в коде

```python
from physics_solvers.pybullet_solver.solverp.solver import PyBulletSolver

solver = PyBulletSolver()
solver.initialize({
    "headless": True,
    "gravity_y": -9.81,
    "fixed_time_step": 0.02,
    "num_sub_steps": 2,
    "solver_iterations": 10,
    "erp": 0.2,
    "contact_erp": 0.2,
    "enable_sleeping": True,
})
solver.step_simulation(0.02)

# Пружинные джойнты мэппят жёсткость в ERP констрейнта:
# changeConstraint(erp=min(1.0, stiffness * 0.01), maxForce=100)
jid = solver.create_joint("spring", body_a, body_b, anchor=(0, 1, 0))
```

Составные тела: `create_compound_rigid_body` делит массу по объёму частей (`shapep.part_volume`), extras линкует через `JOINT_FIXED`; смешанные trigger/solid части пишут warning и делят одно тело.

## Заметки fixed update

- `step_simulation(dt)` ставит `fixedTimeStep = dt / sub_steps`, затем шагает N раз (`solver.py:134-143`).
- `add_plane` строит статическую плоскость + бокс-визуал `100 x 0.1 x 100`.

## Связанные

Culverin Solver, PhysX Solver, Solver & Threading, Joints, Collision Layers.

## Проверено по

- `physics_solvers/pybullet_solver/solverp/solver.py:16-95` (дефолты, `initialize`, мэппинг параметров)
- `physics_solvers/pybullet_solver/solverp/solver.py:134-143` (цикл подшагов `step_simulation`)
- `physics_solvers/pybullet_solver/solverp/solver.py:281-294` (фильтрация коллизий)
- `config/config.py:358-368` (дефолты конфига `2/10/0.2`)
