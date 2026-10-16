# Soft Body

Симуляция деформируемого меша для ткани, желе, мнущегося реквизита — симулируются вершины, меш следует.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `layer` / `mask` | `0` / `0xFFFF` | Фильтрация |
| `mass` | `1.0` | `0.01–10000` |
| `stiffness` | `0.85` | `0–1`; `compliance = (1-stiffness) * 0.01` |
| `bend_mode` | `distance` | `none` / `distance` / `dihedral` |
| `pressure` | `0.0` | Сохранение объёма |
| `damping` | `0.1` | `0–10` |
| `iterations` | `10` | `1–50` |
| `gravity_scale` | `1.0` | `0–5` |
| `vertex_radius` | `0.05` | `0.001–1` |
| `max_velocity` | `500.0` | `1–5000` |
| `pin_mode` | `none` | `none` / `top` / `bottom`, полоса `pin_fraction` |
| `pin_fraction` | `0.1` | `0–0.5` |
| `max_vertices` | `3000` | `0–30000`; воксельная децимация при превышении бюджета (и `max_vertices ≥ 4`) |
| `double_sided` | `True` | |
| `update_com` | `True` | Вести трансформ от центра масс |
| `recompute_normals` | `True` | |
| `material_friction` / `material_bounciness` | `0.2` / `0.0` | Пути ассета у компонента нет |
| Капы | `16` тел, `32768` вершин | `SoftSharedBuffer` |

## Использование в редакторе

`MeshFilter` (источник меша) + `SoftBody`. `Rigidbody` НЕ добавлять — SoftBody владеет сущностью (варнинг, тело пропускается). `top` — шторы, `bottom` — скатерти.

## Использование в коде

```python
entity = scene.create_entity("Cloth")
from core.components.physics.soft_body import SoftBody

sb = SoftBody()
sb.mass = 1.0
sb.stiffness = 0.85
sb.iterations = 10
entity.add_component(sb)  # на сущности нужен MeshFilter с mesh_path
```

Вход солвера: `create_soft_body(entity_id, vertices, indices, position, rotation, mass, compliance, bend_mode, pressure, damping, iterations, gravity_factor, friction, restitution, vertex_radius, max_velocity, max_vertices, pin_mode, pin_fraction, double_sided, update_com, layer, mask)`. Телепорты пересоздают тело; COM-синхронизация пропускается при пинах.

## Заметки о fixed update

- Построение — `_create_entity_soft` из `MeshFilter.mesh_path` через `load_collision_geometry`; синхронизация — `_sync_soft_bodies` / `_sync_soft_mesh` после `step_simulation(dt)` при `dt = Engine._fixed_dt` (`0.02`).
- Пересечения `≥2` soft-тел расталкиваются AABB-импульсом (`_separate_soft_bodies`).

## Связанные

Rigidbody (не комбинировать), Mesh, солвер и потоки (soft-буферы), слои.

## Проверено по

- `core/components/physics/soft_body.py`
- `core/physics/scene/colliders.py`, `scene/events.py`
- `physics_solvers/culverin_solver/solverp/integrate.py`
- `core/physics/soft_shared_buffer.py`
