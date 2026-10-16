# Capsule-коллайдер

Тела персонажей и пилюли: цилиндр со сферическими крышками, не цепляется за швы пола.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `layer` | `0` | `0–15` |
| `mask` | `0xFFFF` | Маска |
| `center` | `(0, 0, 0)` | `scaled_center` |
| `radius` | `0.5` | Мир = `radius * max(scale)` |
| `height` | `2.0` | Мир = `height * скейл_оси` (`scaled_height`) |
| `direction` | `1` | `0` = X, `1` = Y, `2` = Z |
| `is_trigger` | `False` | Только пересечение |
| `physic_material` | `""` | `.zphysmat` |
| `material_friction` / `material_bounciness` | `0.6` / `0.0` | Запасные |

## Использование в редакторе

`Radius`/`Height`, ось `direction` (код/сериализация), Y (`1`) для стоящих персонажей.

## Использование в коде

```python
entity = scene.create_entity("Pill")
from core.components.physics.rigidbody import Rigidbody
from core.components.physics.capsule_collider import CapsuleCollider

rb = Rigidbody()
rb.mass = 80.0
rb.freeze_rot_x = True
rb.freeze_rot_z = True
entity.add_component(rb)
cap = CapsuleCollider()
cap.radius = 0.4
cap.height = 1.8
cap.direction = 1
entity.add_component(cap)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody)
    if rb is not None:
        rb.add_force(Vec3(0.0, 0.0, 50.0))

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Заметки о fixed update

- Вызов: `create_rigid_body(shape_type="capsule", ...)`; `dt` — `Engine._fixed_dt` (`0.02`); проверка формы каждые 60 шагов.

## Связанные

Rigidbody, CharacterController, Box/Sphere, материалы, слои.

## Проверено по

- `core/components/physics/capsule_collider.py`
- `core/physics/shapep/shapes.py`
