# Sphere-коллайдер

Идеальный шар: катание, прыжки, хитбоксы голов, радиальные триггеры.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `layer` | `0` | `0–15` |
| `mask` | `0xFFFF` | Маска столкновений |
| `center` | `(0, 0, 0)` | `scaled_center` |
| `radius` | `0.5` | Инспектор `0.001–10000`; мир = `radius * max(scale)` |
| `is_trigger` | `False` | Только пересечение |
| `physic_material` | `""` | `.zphysmat` |
| `material_friction` | `0.6` | Запасное |
| `material_bounciness` | `0.0` | Запасное |

## Использование в редакторе

`Radius`/`Center`, слой + маска, `Is Trigger` для подборов. Несколько на сущность разрешены.

## Использование в коде

```python
entity = scene.create_entity("Ball")
from core.components.physics.rigidbody import Rigidbody
from core.components.physics.sphere_collider import SphereCollider

rb = Rigidbody()
rb.mass = 2.0
entity.add_component(rb)
s = SphereCollider()
s.radius = 0.5
entity.add_component(s)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody)
    if rb is not None:
        rb.add_force(Vec3(0.0, 0.0, 10.0))
        rb.add_torque(Vec3(1.0, 0.0, 0.0))

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Заметки о fixed update

- Вызов: `create_rigid_body(shape_type="sphere", ...)`. Проверка формы каждые 60 шагов; `dt` — `Engine._fixed_dt` (`0.02`).

## Связанные

Rigidbody, Box/Capsule, материалы, слои.

## Проверено по

- `core/components/physics/sphere_collider.py`
- `core/physics/shapep/shapes.py`
