# Rigidbody 2D

Плоская динамика для скролл-шутеров и top-down — та же форма API, что в 3D, в плоскости держит флаг солвера.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `mass` | `1.0` | `0.001–100000`; при кинематике `0.0` |
| `drag` | `0.0` | `0–1000` |
| `angular_drag` | `0.05` | `0–1000` |
| `gravity_scale` | `1.0` | `0–100` множитель гравитации |
| `is_kinematic` | `False` | Ведом трансформом |
| `freeze_rotation` | `False` | Блокировка спина |
| `velocity` | `Vec2.zero()` | Сеттер помечает грязным |
| `angular_velocity` | `0.0` | Float; сеттер помечает грязным |

## Использование в редакторе

`Rigidbody2D` + `BoxCollider2D`/`CircleCollider2D`. Героям платформеров — `Freeze Rotation`.

## Использование в коде

```python
entity = scene.create_entity("Hero2D")
from core.components.physics2d.rigidbody2d import Rigidbody2D
from core.components.physics2d.box_collider2d import BoxCollider2D

rb = Rigidbody2D()
rb.mass = 1.0
rb.gravity_scale = 1.0
rb.freeze_rotation = True
entity.add_component(rb)
entity.add_component(BoxCollider2D())

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody2D)
    if rb is None or rb.is_kinematic:
        return
    rb.add_force(Vec2(0.0, 30.0))  # очищается после пуша; каждый шаг
    rb.add_torque(1.0)
    rb.add_impulse(Vec2(0.0, 5.0))

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Заметки о fixed update

- `dt = Engine._fixed_dt` (`0.02`); воркер обнуляет `vz` и наклоны после `step_simulation(dt)`; кинематика/грязные при чтении пропускаются.

## Связанные

Box/Circle Collider 2D, Rigidbody (3D), слои, солвер и потоки.

## Проверено по

- `core/components/physics2d/rigidbody2d.py`
- `core/physics/scene/scene.py`
- `core/physics/physics_worker.py`
