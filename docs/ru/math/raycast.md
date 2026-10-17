# Рейкастинг

Три уровня, внизу везде Мёллер–Трамбор с кодом промаха `-1.0`:

| Уровень | Вход | Возврат |
|---|---|---|
| Треугольник | `math_helpers.ray_triangle_intersect(...)` | `t` или `-1.0` (порог параллельности `|det| < 1e-12`; барицентрический отсев; `t <= 0` отбрасывается) |
| BVH меша | `BVH.intersect(...)` / `BVH.intersect_any(...)` | ближайшее `t` / `bool` (slab-предтест `1e-30`, тест треугольника `1e-12`) |
| Octree сцены | `Octree.raycast(origin, direction, max_dist=100.0)` | `[(entity_id, t)]`, отсортировано по `t` |

Вспомогательные касты: `ray_mesh_intersect` (ближайшее по индексным тройкам), `ray_aabb_intersect` (slab, сентинелы `±1e30`, осевой порог `1e-30`), `ray_sphere_intersect_batch` (пакетный SoA), `AABB.intersects_ray(origin, direction, max_dist)` (осевой порог `|d| < 1e-12`).

Дефолты `Octree`: `Octree(world_size=500.0, max_depth=8, max_objects=8)`; инстанс сцены — `world_size=1000.0`. `Octree.update_xyz(entity_id, cx, cy, cz, rad)` — быстрое перемещение на месте, возвращает `False`, если бокс покинул свой узел. Дефолт `AABB` — `±0.5` (`min=(-0.5,-0.5,-0.5)`, `max=(0.5,0.5,0.5)`).

## Скрипт выбора объекта

```python
from core.maths.math3d import Vec3
from core.spatial import AABB, Octree

octree = Octree(world_size=500.0)
octree.insert("enemy-1", AABB(Vec3(-1, -1, -1), Vec3(1, 1, 1)))

origin, direction = Vec3(0, 0, 5), Vec3(0, 0, -1)
hits = octree.raycast(origin, direction, max_dist=100.0)  # [("enemy-1", t)]
if hits:
    picked, dist = hits[0]
    print(picked, dist)

# Эквивалент уровня сцены:
hit = scene.spatial_raycast(Vec3(0, 0, 5), Vec3(0, 0, -1), 100.0)

# Сырой уровень треугольника / меша:
from core import math_helpers as mh
t = mh.ray_triangle_intersect(0, 0, 5, 0, 0, -1,
                              -1, -1, 0, 1, -1, 0, 0, 1, 0)
print(t)  # 5.0, при промахе -1.0

from core.spatial.bvhv.bvh import get_mesh_bvh
bvh = get_mesh_bvh(vertices, indices)
t2 = bvh.intersect(0, 0, 5, 0, 0, -1, vertices, indices)
any_hit = bvh.intersect_any(0, 0, 5, 0, 0, -1, vertices, indices)
```

## Связанные страницы

- [Обзор математики](overview.md)
- [Math Helpers](../core/advanced/math-helpers.md), [Пространство](../core/advanced/spatial.md)
- [Точность и 64 бит](../core/advanced/precision-64bit.md)

## Проверено по

- `core/math_helpers.py:303-321` (`ray_triangle_intersect`), `:324-335` (`ray_mesh_intersect`), `:338-365` (`ray_aabb_intersect`), `:187-211` (пакет сфер)
- `core/spatial/octp/tree.py:13-18` (дефолты), `:20-45` (`update_xyz`), `:138-142` (`raycast`)
- `core/spatial/octp/geometry.py:13-19` (дефолты `AABB`), `:58-81` (`intersects_ray`)
- `core/spatial/bvhv/query.py:86-150` (`intersect`), `:151-214` (`intersect_any`), `:56-83` (Мёллер `1e-12`)
