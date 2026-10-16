# Пространственные структуры

> Пространственные запросы по сцене работают на `core/spatial`: `Octree` AABB сущностей, принадлежащий `Scene` (`world_size=1000.0`), `BVH` на каждый меш с рейкаст-запросами и frustum-culling батч-хелперы в `core/pyx/`. `Scene` отдаёт их как `spatial_query` / `spatial_raycast`.

[Home](../../../index.md) | [Section index](./_section.md)

## Обзор

- `Octree` (`core/spatial/octp/tree.py`, реэкспорт из `core/spatial/__init__.py` вместе с `AABB`, `OctreeNode`): конструктор `(world_size=500.0, max_depth=8, max_objects=8)`; `Scene` владеет деревом с `world_size=1000.0`. API: `insert(entity_id, aabb)` (авто-подразбиение полных листьев по октанту центра; повторная вставка заменяет), `remove(entity_id)`, `update_xyz(entity_id, cx, cy, cz, rad)` (быстрое перемещение на месте, `False` если бокс ушёл из ноды), `query(aabb)`, `query_point(point)`, `raycast(origin, direction, max_dist=100.0)` → `[(entity_id, t)]`. `OctreeNode` (`geometry.py`) реализует рекурсивные примитивы.
- Клей сцены (`core/ecs/scene_spatial.py` + обёртки `Scene`): сброс dirty-трансформов вставляет/обновляет боксы сущностей (приблиз. ±2.5 единицы вокруг позиции), `rebuild_spatial()`, `spatial_query(aabb)`, `spatial_raycast(origin, direction, max_dist)`, `mark_spatial_dirty()`; `entity.py` удаляет id при удалении сущности.
- `BVH` (`core/spatial/bvhv/`): ускорение на меш по `vertices`/`indices` (строки нод `float32`, пересборка `from_cache`, `flatten_for_gpu` / `flatten_for_gpu_stackless`, `node_depths`, `tri_indices`, `node_count`, `depth`). Запросы (`query.py`): `intersect(ox..., vertices, indices)` → ближайшее `t` (Мёллер–Трумбор + slab-предтест), `intersect_any` → bool, `build_bvh_arrays`. Построение (`build.py`): SAH `_build` плюс Morton LBVH `_build_lbvh` при режиме `"fast"` и числе треугольников > 4096; `lru.py` добавляет LRU/дисковый кэш (`_build_bvh`, `get_mesh_bvh`, `prebuild_mesh_bvh`). Это уровень меша (рейкаст/пикинг), а не октодерево сущностей.
- Каллинг (`core/pyx/`): `_culling.pyx` (`cpu_frustum_cull`, `cpu_frustum_cull_parallel`), `_render_utils.pyx` (`build_frustum_cull_inputs` — сферные данные для GPU-каллинга), `_shadow_batch.pyx` (`frustum_cull_shadow_groups`).

## Свойства

| структура | охват | ключевой API |
|---|---|---|
| `Octree` | сущности сцены | `insert` / `remove` / `update_xyz` / `query` / `query_point` / `raycast` |
| обёртки `Scene` | удобство сцены | `spatial_query` / `spatial_raycast` / `rebuild_spatial` / `mark_spatial_dirty` |
| `BVH` | один меш | `intersect` / `intersect_any` / `flatten_for_gpu` / `get_mesh_bvh` |
| каллинг-батчи | списки рендера | `cpu_frustum_cull(_parallel)`, `build_frustum_cull_inputs`, `frustum_cull_shadow_groups` |

## Использование в редакторе

Пикинг вьюпорта и гизмо-рейкасты идут через пространственные запросы; импорт мешей умеет предсобирать BVH (`prebuild_mesh_bvh`), чтобы редактор не подвисал на первом пике.

## Использование в коде

```python
from core.maths.math3d import Vec3
from core.spatial import AABB, Octree

octree = Octree(world_size=500.0)
octree.insert("enemy-1", AABB(Vec3(-1, -1, -1), Vec3(1, 1, 1)))
print(octree.query_point(Vec3(0, 0, 0)))          # ["enemy-1"]
print(octree.raycast(Vec3(0, 0, 5), Vec3(0, 0, -1)))

hits = scene.spatial_query(AABB(Vec3(-5, -5, -5), Vec3(5, 5, 5)))
hit = scene.spatial_raycast(Vec3(0, 0, 5), Vec3(0, 0, -1), 100.0)

from core.spatial.bvhv.bvh import get_mesh_bvh
bvh = get_mesh_bvh(vertices, indices)
t = bvh.intersect(0, 0, 5, 0, 0, -1, vertices, indices)
```

## Сериализация

Пространственные структуры никогда не сериализуются; пересобираются из трансформов сущностей (`rebuild_spatial`) и ассетов мешей при загрузке.

## Связанные страницы

- [Обзор ECS](../ecs-overview.md)
- [Сущность Entity](../entity.md)
- [Math-хелперы](./math-helpers.md)
- [Точность и 64 бита](./precision-64bit.md)

## Проверено по

- `core/spatial/__init__.py` (реэкспорт `AABB`, `OctreeNode`, `Octree`)
- `core/spatial/octp/tree.py` (дефолты, `insert`/`remove`/`update_xyz`/`query`/`query_point`/`raycast`)
- `core/spatial/octp/geometry.py` (примитивы `OctreeNode`)
- `core/ecs/scene.py` (`_spatial = Octree(world_size=1000.0)`, обёртки `spatial_query`/`spatial_raycast`)
- `core/ecs/scene_spatial.py` (клей flush/insert/rebuild/query)
- `core/ecs/entity.py` (`_spatial.remove` при удалении)
- `core/spatial/bvhv/bvh.py`, `query.py`, `build.py`, `lru.py` (BVH меша + запросы + кэш)
- `core/pyx/_culling.pyx`, `_render_utils.pyx`, `_shadow_batch.pyx` (frustum-culling ядра), `_octree_batch.pyx` (`aabb_*_batch`), `_bvh_build.pyx` (`sah_compute_best_split`)
