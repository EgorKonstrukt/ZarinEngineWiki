# Обзор физики 3D

3D-симуляция твёрдых тел ZarinEngine: сущности с `Rigidbody` и коллайдерами шагаются подключаемым солвером (`IPhysicsSolver`), всё остальное — статичные декорации.

## Свойства

| Настройка | По умолчанию | Где |
|---|---|---|
| Солвер | `culverin` (Jolt через пакет `culverin`) | `physics_solvers/registry.py` `DEFAULT_SOLVER`, `physics.solver` |
| Гравитация | `(0.0, -9.81, 0.0)` | `physics.gravity_x/y/z`, `CulverinSolver._gravity` |
| Фиксированный шаг | `0.02` с | `engine.fixed_update_dt`, `physics.fixed_time_step`, `Engine._fixed_dt` |
| Подшаги | `2` | `physics.num_sub_steps` |
| Итерации солвера | `10` | `physics.solver_iterations` |
| Режим симуляции | `multi_threaded` | `single` / `multi_threaded` / `per_layer_process` |

## Использование в редакторе

1. Статичный пол: только `BoxCollider` (без `Rigidbody`).
2. Динамический ящик: `Rigidbody` (масса `1.0`) + `BoxCollider` (`1,1,1`).
3. Нажать Play. Кинематические движители: `is_kinematic` + движение трансформом; триггеры: `is_trigger` + колбэки пересечений.

## Использование в коде

```python
entity = scene.create_entity("Crate")
from core.components.physics.rigidbody import Rigidbody
from core.components.physics.box_collider import BoxCollider
from core.maths.math3d import Vec3

rb = Rigidbody()
rb.mass = 1.0
entity.add_component(rb)
box = BoxCollider()
box.size = Vec3(1.0, 1.0, 1.0)
entity.add_component(box)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody)
    if rb is not None:
        rb.add_force(Vec3(0.0, 10.0, 0.0))
        rb.add_torque(Vec3(0.0, 1.0, 0.0))

def on_collision_enter(self, other_id, force=0.0):
    pass  # (id_сущности, сила_удара), также on_collision_stay/exit
```

Заморозка осей: `rb.freeze_pos_x/y/z`, `rb.freeze_rot_x/y/z` (сериализация `freeze_pos` / `freeze_rot`).

## Заметки о fixed update

- `Engine.tick_fixed_step` (`core/engine/eng/loop.py`): накопление реального времени по `_fixed_dt` (`0.02`), потолок backlog `5 * _fixed_dt`, максимум `5` шагов за тик (`MAX_FIXED_STEPS`), затем `scene.fixed_update(fd)` (компонентный `on_fixed_update`: силы/приводы `Buoyancy`/`Motor`/`CharacterController`) и наконец системный `step(fd)` плагинов.
- Порядок `PhysicsScene.step`: регистрация новых сущностей → проверка форм (каждые 60 шагов) → синхронизация ECS→физика → AABB-разделение soft-тел (только при `≥2` soft-телах) → `solver.step_simulation(dt)` → 2D-ограничение → физика→ECS → soft-синхронизация → события коллизий.
- Область параметров: `num_sub_steps` (`2`), `solver_iterations` (`10`), `erp` (`0.2`) — дефолты конфига проекта, потребляемые PyBullet-солвером (его собственные дефолты `1` / `50` / `0.4`); дефолтный Culverin/Jolt их игнорирует и использует капы мира `culverin_*`.
- Колбэки коллизий: скриптовые методы принимают `(other_id)` или `(other_id, force)` — арность определяется по `inspect.signature` (`ScriptComponent._py_collision_arity`); хендлеры движковых компонентов всегда получают `(other_id, force)`.
- Потоки: `multi_threaded` — солвер в демоне `multiprocessing.Process` (`PhysicsProcess`) через `SharedPhysicsBuffer` / `SoftSharedBuffer`; `single` — в процессе (требуется для `CharacterController` / `Motor`); `per_layer_process` — процессы по слоям.

## Связанные

Rigidbody, коллайдеры Box/Sphere/Capsule/Mesh/Terrain/GSVolume, CharacterController, Joints, Motor, материалы, слои, солвер и потоки, Buoyancy, Soft Body, PhysBone, Constraints (Transform-последователи — отдельная система, не солверные джойнты).

## Проверено по

- `core/physics/scene/scene.py`, `core/physics/scene/events.py`
- `core/engine/eng/loop.py`, `core/engine/eng/engine.py`
- `core/config/config.py`, `physics_solvers/registry.py`, `plugins/physics_plug/plugin.py`
