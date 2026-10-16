# Гайд 2D

Плоская физика для топ-дауна/сайд-вью. 2D-тела используют `Rigidbody2D` + один 2D-коллайдер, считаются общим солвером и каждый шаг прибиваются к плоскости.

## Свойства

| Поле | Дефолт | Примечания |
|---|---|---|
| `Rigidbody2D.mass` | `1.0` | `add_impulse` делит на массу, если не kinematic |
| `drag` | `0.0` | Инспектор `0–1000` |
| `angular_drag` | `0.05` | Инспектор `0–1000` |
| `gravity_scale` | `1.0` | Инспектор `0–100` |
| `is_kinematic` | `False` | |
| `freeze_rotation` | `False` | Включите для топ-даун персонажей |
| `velocity` / `angular_velocity` | `Vec2.zero()` / `0.0` | Сеттеры ставят `_velocity_dirty`; `consume_velocity_dirty()` сбрасывает |

Мэппинг шейпов (`shapep/shapes.py:83-91`): `BoxCollider2D → box` размер `[sx, sy, 1.0]`, центр `[ox, oy, 0.0]`; `CircleCollider2D → sphere` радиус `scaled_radius`, центр `[ox, oy, 0.0]`. Не мешайте 2D- и 3D-компоненты на одной сущности.

Прибивка к плоскости (`scene.py:212-227`, `_constrain_2d_bodies`): после `step_simulation(dt)` каждое 2D-тело клэмпится — `pos.z → 0`, ротация `(0, 0, rz)`, скорость `(vx, vy, 0)`, угловая `(0, 0, az)`.

## Использование в редакторе

Статичная земля: только `BoxCollider2D`. Игрок: `Rigidbody2D` + `BoxCollider2D` с включённым `Freeze Rotation`. Монеты: триггерный `CircleCollider2D` с репортом через `on_collision_enter`.

## Использование в коде

```python
scene = Engine.instance().scene
player = scene.create_entity("Player2D")
from core.components.physics2d.rigidbody2d import Rigidbody2D
from core.components.physics2d.box_collider2d import BoxCollider2D
from core.maths.math3d import Vec2

rb = Rigidbody2D()
rb.mass = 1.0
rb.gravity_scale = 0.0      # топ-даун: гравитации нет
rb.freeze_rotation = True   # не крутится от ударов
player.add_component(rb)
player.add_component(BoxCollider2D())

ground = scene.create_entity("Ground2D")
ground.add_component(BoxCollider2D())  # статика: только коллайдер

# тяга из скрипта (например, в on_fixed_update):
# rb.add_force(Vec2(10.0, 0.0))
# rb.add_impulse(Vec2(0.0, 5.0))
```

## Заметки fixed update

- Тот же `dt = Engine._fixed_dt` (`0.02` по умолчанию), что в 3D; `_constrain_2d_bodies` работает после шага солвера.
- Те же колбэки `on_collision_enter/stay/exit` и фильтрация `layer`/`mask`, что в 3D.

## Связанные

Rigidbody2D, Box/Circle Collider 2D, Collision Callbacks, Collision Layers.

## Проверено по

- `core/components/physics2d/rigidbody2d.py:12-104` (поля, дефолты, API сил/импульсов)
- `core/physics/shapep/shapes.py:83-97` (`BoxCollider2D→box [sx,sy,1.0]`, `CircleCollider2D→sphere`)
- `core/physics/scene/scene.py:212-227` (клэмп `_constrain_2d_bodies`)
