# Мотор Motor

Постоянный привод для конвейеров, колёс, вентиляторов, ловушек, лифтов и серво-дверей — ровное движение без скриптовой скорости каждый кадр.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `connected_entity_id` | `""` | Другая сущность **по id** (у `Joint` — по имени) |
| `motor_type` | `hinge` | `hinge` / `slider` |
| `mode` | `position` | `position` (серво к `target`) / `velocity` (интеграция `target_velocity`) |
| `axis` | `(0, 0, 1)` | Ось привода |
| `anchor` | `(0, 0, 0)` | Якорь связи |
| `target` | `0.0` | Целевая позиция (или сид бегущей скорости) |
| `target_velocity` | `0.0` | `running += target_velocity * dt` в velocity-режиме |
| `motor_force` / `motor_torque` | `500.0` / `500.0` | Уходят как `max_force`/`max_torque` |
| `limit_low` / `limit_high` | `-100000.0` / `100000.0` | По умолчанию широко открыты |
| `free_spin` | `False` | Хранимый флаг (создание связи его не читает; `stiffness`/`damping` передаются как `0.0`) |

## Использование в редакторе

Обоим телам — `Rigidbody` + коллайдер; добавить `Motor`, связать `Connected Entity` (id), выбрать `hinge`/`slider` и `position`/`velocity`, задать силу/момент и лимиты.

## Использование в коде

```python
entity = scene.create_entity("Wheel")
from core.components.physics.motor import Motor, MotorType, MotorMode
from core.maths.math3d import Vec3

m = Motor()
m.connected_entity_id = chassis_id
m.motor_type = MotorType.HINGE
m.mode = MotorMode.VELOCITY
m.axis = Vec3(0.0, 0.0, 1.0)
m.target_velocity = 5.0
m.motor_force = 500.0
entity.add_component(m)
m.set_target(1.57)
m.set_mode(MotorMode.POSITION)
```

Связь создаётся в `on_start`/`on_enable` через `solver.create_joint` + `enable_constraint`; удаляется в `on_disable`.

## Заметки о fixed update

- `on_fixed_update(dt)` при `dt = Engine._fixed_dt` (`0.02`): velocity-режим пушит `set_motor_target(running)` каждый шаг; position — перепушивает `target`.
- Требует режим `single` (`_resolve_solver` → `plugin.ensure_single_mode()`), как `CharacterController`.

## Связанные

Joints, Rigidbody, солвер и потоки (требование режима), Constraints (Transform-последователи — отдельная система, не солверные джойнты).

## Проверено по

- `core/components/physics/motor.py`
- `core/physics/physics_solver.py`
