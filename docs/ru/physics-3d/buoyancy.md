# Плавучесть Buoyancy

Плавающие тела для прудов, рек и океанов: корпуса вытесняют воду, волны качают, течения толкают. CPU-сэмплинг в `on_fixed_update`, не тело солвера.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `density` | `500.0` | `1–20000` |
| `volume` | `0.0` | Оверрайд; `0` = авто из коллайдера (box `sx*sy*sz`, sphere `4/3πr³`, capsule `4/3πr³ + πr²h`) |
| `water_density` | `1000.0` | `1–2000` |
| `linear_drag` | `8.0` | `0–50` (фолбэк десериализации `1.5`) |
| `angular_drag` | `6.0` | `0–50` (фолбэк `1.2`) |
| `flow_influence` | `1.0` | `0–10` |
| `use_waves` / `use_flow` | `True` / `True` | `height_at` / `flow_at` из `WaterVolume` |
| `sample_resolution` | `8` | Сетка `2–24` на ось |
| `max_acceleration` | `8.0` | Клапм × g |
| `enable_debug` | `False` | Точки сэмплов в `gizmo_lines` |
| Только чтение | `submerged_fraction` | `0–1` |

## Использование в редакторе

`Rigidbody` (не кинематика) + `Box`/`Sphere`/`CapsuleCollider` + `Buoyancy`, в сцене — `WaterVolume`. `density` vs `water_density`, `linear_drag` — чувство корпуса.

## Использование в коде

```python
entity = scene.create_entity("Boat")
from core.components.physics.rigidbody import Rigidbody
from core.components.physics.box_collider import BoxCollider
from core.components.physics.buoyancy import Buoyancy

rb = Rigidbody()
rb.mass = 500.0
entity.add_component(rb)
entity.add_component(BoxCollider())
b = Buoyancy()
b.density = 500.0
b.linear_drag = 8.0
b.sample_resolution = 8
entity.add_component(b)
# В on_fixed_update компонент сам считает Архимеда + drag + течение,
# кламп max_acceleration * 9.81, applied через rb.add_force/add_torque.
```

Без коллайдера-формы построение падает, без `WaterVolume` — `submerged_fraction` всегда `0`.

## Заметки о fixed update

- Работает в `on_fixed_update(dt)` при `dt = Engine._fixed_dt` (`0.02`); сила уходит через `rb.add_force`/`add_torque` на следующий пуш. Пропуск при кинематике. Внутренняя гравитация `_GRAVITY_MAG = 9.81`.

## Связанные

Rigidbody, Box/Sphere/Capsule, WaterVolume, солвер и потоки.

## Проверено по

- `core/components/physics/buoyancy.py`
