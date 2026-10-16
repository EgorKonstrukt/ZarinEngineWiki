# Обзор физики 2D

Отдельный лёгкий 2D-конвейер со своими компонентами (`Rigidbody2D`, `BoxCollider2D`, `CircleCollider2D`), симулируется тем же солвером, но с флагом 2D: внеплоскостные скорости и наклоны обнуляются каждый шаг.

## Свойства

| Элемент | Значение |
|---|---|
| Тела | `Rigidbody2D` + один 2D-коллайдер; не смешивать 2D и 3D на одной сущности |
| Маппинг форм | `BoxCollider2D → box` размер `[sx, sy, 1.0]` центр `[ox, oy, 0]`; `CircleCollider2D → sphere` |
| Блокировка плоскости | `z = 0`, поворот `(0, 0, rz)`, скорость `(vx, vy, 0)`, спин `(0, 0, az)` |
| Колбэки / слои | Те же `on_collision_enter/stay/exit` и фильтрация `layer`/`mask`, что в 3D |

## Использование в редакторе

Статичная земля: только `BoxCollider2D`. Игрок: `Rigidbody2D` + `BoxCollider2D` с `Freeze Rotation`. Монеты: триггер `CircleCollider2D`.

## Использование в коде

```python
entity = scene.create_entity("Player2D")
from core.components.physics2d.rigidbody2d import Rigidbody2D
from core.components.physics2d.box_collider2d import BoxCollider2D

rb = Rigidbody2D()
rb.mass = 1.0
rb.freeze_rotation = True
entity.add_component(rb)
entity.add_component(BoxCollider2D())

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody2D)
    if rb is not None:
        rb.add_force(Vec2(10.0, 0.0))

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Заметки о fixed update

- Тот же `dt = Engine._fixed_dt` (`0.02`) и тот же потоковый путь, что в 3D; `_constrain_2d_bodies` клампит трансформ/скорость после `step_simulation(dt)`; флаг shared-памяти `8` — 2D-тела.

## Связанные

Rigidbody2D, Box/Circle Collider 2D, обзор 3D, слои, солвер и потоки.

## Проверено по

- `core/components/physics2d/`
- `core/physics/scene/scene.py`
- `core/physics/shapep/shapes.py`
