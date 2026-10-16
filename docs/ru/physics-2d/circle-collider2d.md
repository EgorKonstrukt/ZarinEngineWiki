# Circle Collider 2D

Диск в плоскости: мячи, колёса, монеты, радиальные триггеры.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `layer` / `mask` | `0` / `0xFFFF` | Фильтрация |
| `offset` | `(0, 0)` | `Vec2`; мировой центр `[ox*sx, oy*sy, 0]` |
| `radius` | `0.5` | `0.001–10000`; мир = `radius * max(sx, sy)` |
| `is_trigger` | `False` | Только пересечение |
| `physic_material` | `""` | `.zphysmat` |
| `material_friction` / `material_bounciness` | `0.6` / `0.0` | Запасные |

## Использование в редакторе

`Radius`/`Offset`, слой + маска, `Is Trigger` для подборов.

## Использование в коде

```python
entity = scene.create_entity("Coin")
from core.components.physics2d.rigidbody2d import Rigidbody2D
from core.components.physics2d.circle_collider2d import CircleCollider2D

c = CircleCollider2D()
c.radius = 0.3
c.is_trigger = True
entity.add_component(c)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody2D)
    if rb is not None:
        rb.add_force(Vec2(5.0, 0.0))

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Заметки о fixed update

- Вызов: `create_rigid_body(shape_type="sphere", shape_params={"radius", "center": [ox, oy, 0.0]})`; `dt = Engine._fixed_dt` (`0.02`).

## Связанные

Rigidbody 2D, Box 2D, Sphere (3D), слои.

## Проверено по

- `core/components/physics2d/circle_collider2d.py`
- `core/physics/shapep/shapes.py`
