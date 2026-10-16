# Джойнты

Соединяют два тела связью: двери, колёса, подвеска, конечности рэгдолла, ломаемые крепления.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `connected_entity_name` | `""` | Другая сущность **по имени** |
| `anchor` | `(0, 0, 0)` | Мировой якорь |
| `axis` | `(0, 0, 1)` | Ось hinge/slider |
| `joint_type` | `"hinge"` | `hinge` / `fixed` / `spring` |
| `limit_low` / `limit_high` | `-3.14159` / `3.14159` | Радианы |
| `stiffness` / `damping` | `10.0` / `1.0` | Пружинные крепления |

## Использование в редакторе

Обеим сущностям — `Rigidbody` + коллайдер; одной — `Joint`; в `Connected Entity` вписать **имя** второй; якорь/ось/лимиты.

## Использование в коде

```python
entity = scene.create_entity("Door")
from core.components.physics.joint import Joint
from core.maths.math3d import Vec3

j = Joint()
j.connected_entity_name = "Frame"
j.joint_type = "hinge"
j.anchor = Vec3(0.0, 1.0, 0.0)
j.axis = Vec3(0.0, 0.0, 1.0)
j.limit_low = -1.57
j.limit_high = 1.57
entity.add_component(j)

def on_collision_enter(self, other_id, force=0.0):
    pass
```

Маппинг солвера: `fixed` → fixed, `point`/`point2point` → point, `hinge`/`revolute` → hinge, `slider`/`prismatic` → slider, `distance`/`spring` → distance, `cone`/`spherical` → cone. Хелперы: `create_drag_constraint`, `create_fixed_constraint`, `create_hinge_constraint`, `remove_joint_constraint`.

## Заметки о fixed update

- Джойнты создаются при загрузке сцены; отсутствующие имя/тело — варнинг и пропуск. Решение связей внутри `solver.step_simulation(dt)` при `dt = Engine._fixed_dt` (`0.02`).

## Связанные

Rigidbody, Motor, слои (тела должны иметь общую пару), солвер и потоки, Constraints (Transform-последователи — отдельная система, не солверные джойнты).

## Проверено по

- `core/components/physics/joint.py`, `core/physics/scene/joints.py`
- `core/physics/physics_solver.py`
- `physics_solvers/culverin_solver/solverp/broadphase.py`
