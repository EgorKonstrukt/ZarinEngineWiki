# Твёрдое тело Rigidbody

Делает сущность симулируемой: масса, реакция на гравитацию, накопление сил, читаемые скорости. Без него коллайдер — статичные декорации.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `mass` | `1.0` | Инспектор `0.001–100000`; при `is_kinematic` форсируется `0.0` |
| `drag` | `0.0` | `0–1000` |
| `angular_drag` | `0.05` | `0–1000` |
| `use_gravity` | `True` | Гравитация применяется |
| `is_kinematic` | `False` | Ведом трансформом: толкает динамику, сам не толкаем |
| `freeze_pos_x/y/z` | `False` | Сериализация `freeze_pos` |
| `freeze_rot_x/y/z` | `False` | Сериализация `freeze_rot` |
| `velocity` / `angular_velocity` | `Vec3.zero()` | Сеттер помечает тело грязным для следующей синхронизации |

## Использование в редакторе

`Rigidbody` + коллайдер. Массы: игрок `70–90`, ящики `1–10`; держите контактирующие массы в пределах ~100:1. `is_kinematic` — движители, заморозка вращений — непадающие ящики.

## Использование в коде

```python
entity = scene.create_entity("Ball")
from core.components.physics.rigidbody import Rigidbody
from core.components.physics.sphere_collider import SphereCollider

rb = Rigidbody()
rb.mass = 2.0
rb.drag = 0.1
rb.freeze_rot_x = True
rb.freeze_rot_z = True
entity.add_component(rb)
col = SphereCollider()
col.radius = 0.5
entity.add_component(col)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody)
    if rb is None or rb.is_kinematic:
        return
    rb.add_force(Vec3(0.0, 20.0, 0.0))   # копится, очищается после отправки
    rb.add_torque(Vec3(0.0, 1.0, 0.0))
    rb.add_impulse(Vec3(0.0, 5.0, 0.0))  # мгновенно: импульс/масса

def on_collision_enter(self, other_id, force=0.0):
    pass
def on_collision_stay(self, other_id, force=0.0):
    pass
def on_collision_exit(self, other_id, force=0.0):
    pass
```

- `add_force(force, world_space=True)` / `add_torque` — вызывать каждый fixed-шаг для постоянной тяги.
- `add_impulse` игнорируется для кинематики и неположительной массы.
- Колбэки коллизий: скриптовые `on_collision_enter/stay/exit` принимают `(other_id)` или `(other_id, force)` (арность определяется автоматически); хендлеры движковых компонентов всегда принимают `(other_id, force)`.

## Заметки о fixed update

- `dt` — это `Engine._fixed_dt` (`0.02`); лаги рендера на `dt` не влияют (потолок `5 * _fixed_dt`, макс. `5` шагов/тик).
- Силы уходят в `_sync_ecs_to_physics`, результаты возвращаются в `_sync_physics_to_ecs`; кинематика и грязные трансформы при чтении пропускаются.
- В `multi_threaded` обмен идёт через `SharedPhysicsBuffer` (флаги `1` active, `2` dirty, `4` kinematic/teleport, `8` 2D).

## Связанные

Коллайдеры Box/Sphere/Capsule/Mesh, CharacterController, Joints, солвер и потоки, слои.

## Проверено по

- `core/components/physics/rigidbody.py`
- `core/physics/scene/bodies.py`, `core/physics/scene/events.py`
- `core/engine/eng/loop.py`, `core/engine/eng/engine.py`
