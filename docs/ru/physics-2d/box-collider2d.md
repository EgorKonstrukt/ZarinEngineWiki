# Box Collider 2D

Прямоугольник в плоскости: платформы, стены, ящики, хитбоксы и триггер-зоны.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `layer` / `mask` | `0` / `0xFFFF` | Фильтрация |
| `offset` | `(0, 0)` | `Vec2`; мировой центр `[ox*sx, oy*sy, 0]` |
| `size` | `(1, 1)` | `Vec2`; размер солвера `[sx, sy, 1.0]` |
| `is_trigger` | `False` | Только пересечение |
| `physic_material` | `""` | `.zphysmat` |
| `material_friction` / `material_bounciness` | `0.6` / `0.0` | Запасные |

## Использование в редакторе

`Size`/`Offset`, слой + маска, `Is Trigger` для зон.

## Использование в коде

```python
entity = scene.create_entity("Platform")
from core.components.physics2d.box_collider2d import BoxCollider2D
from core.maths.math3d import Vec2

box = BoxCollider2D()
box.size = Vec2(4.0, 0.5)
box.offset = Vec2(0.0, 0.0)
box.is_trigger = False
entity.add_component(box)  # без Rigidbody2D = статика

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Заметки о fixed update

- Вызов: `create_rigid_body(shape_type="box", shape_params={"size": [sx, sy, 1.0], "center": [ox, oy, 0.0]})`; `dt = Engine._fixed_dt` (`0.02`).

## Связанные

Rigidbody 2D, Circle 2D, Box (3D), слои.

## Проверено по

- `core/components/physics2d/box_collider2d.py`
- `core/physics/shapep/shapes.py`
