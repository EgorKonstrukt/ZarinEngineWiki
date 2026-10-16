# Box-коллайдер

Осевой бокс в локальном пространстве. Самая дешёвая форма любого солвера — ящики, стены, полы, триггеры.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `layer` | `0` | Слой `0–15` |
| `mask` | `0xFFFF` | Маска столкновений |
| `center` | `(0, 0, 0)` | Масштабируется скейлом (`scaled_center`) |
| `size` | `(1, 1, 1)` | Полные габариты × скейл (`scaled_size`) |
| `is_trigger` | `False` | Только отчёт о пересечении |
| `physic_material` | `""` | Путь `.zphysmat`; при назначении побеждает |
| `material_friction` | `0.6` | Запасное значение |
| `material_bounciness` | `0.0` | Запасное значение |

## Использование в редакторе

`Size`/`Center`, `Layer` + `Collision Mask`, `Is Trigger` для сенсоров. Несколько компонентов на сущность разрешены (составное тело).

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
box.center = Vec3(0.0, 0.5, 0.0)
box.layer = 0
box.mask = 0xFFFF
entity.add_component(box)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody)
    if rb is not None:
        rb.add_force(Vec3(5.0, 0.0, 0.0))

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Заметки о fixed update

- Ключ формы (`size`, `center`, трение, упругость, триггер) перепроверяется каждые 60 шагов; изменения пересоздают тело (`rebuild_entity`).
- Вызов солвера: `create_rigid_body(shape_type="box", ...)`; несколько примитивов объединяются в `create_compound_rigid_body`.
- `dt` — `Engine._fixed_dt` (`0.02`).

## Связанные

Rigidbody, Sphere/Capsule/Mesh, материалы, слои, солвер и потоки.

## Проверено по

- `core/components/physics/box_collider.py`
- `core/physics/shapep/shapes.py`
- `core/physics/scene/bodies.py`
