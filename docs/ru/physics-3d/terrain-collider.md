# Terrain-коллайдер

Heightfield-коллизия по данным террейна: гладкий высотный сэмплинг вместо миллионов треугольников.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `layer` / `mask` | `0` / `0xFFFF` | Фильтрация |
| `size` | `(1000.0, 60.0, 1000.0)` | Мировой размер `(x, y, z)` |
| `height_scale` | `60.0` | Вертикальный масштаб |
| `resolution` | `256` | Сэмплов на сторону; `set_height_data` обновляет из формы массива |
| `center` | `(0, 0, 0)` | Смещение |
| `is_trigger` | `False` | Только пересечение |
| `physic_material` | `""` | `.zphysmat` |
| `material_friction` | `0.9` | Выше, чем у остальных коллайдеров |
| `material_bounciness` | `0.0` | |
| `height_data` | `None` | 2D `np.ndarray` высот |

## Использование в редакторе

`World Size`, `Height Scale`, `Resolution`, высоты из системы террейна (`set_height_data`), слой/маска.

## Использование в коде

```python
entity = scene.create_entity("Ground")
from core.components.physics.terrain_collider import TerrainCollider
import numpy as np

tc = TerrainCollider()
tc.size = Vec3(1000.0, 60.0, 1000.0)
tc.height_scale = 60.0
tc.set_height_data(np.zeros((256, 256), dtype=np.float32))
entity.add_component(tc)  # статика: без Rigidbody

def on_collision_enter(self, other_id, force=0.0):
    pass
```

`cell_size()` = `(size.x/(res-1), size.z/(res-1))`.

## Заметки о fixed update

- Вызов: `create_rigid_body(shape_type="heightfield", ...)`; Culverin децимирует сетки выше `8000` вершин, без данных — варнинг. `heightfield`-довески всегда статичны. `dt` — `Engine._fixed_dt` (`0.02`).

## Связанные

Rigidbody, Mesh, материалы, слои.

## Проверено по

- `core/components/physics/terrain_collider.py`
- `core/physics/shapep/shapes.py`
- `physics_solvers/culverin_solver/solverp/solver.py`
