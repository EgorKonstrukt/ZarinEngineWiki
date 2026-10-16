# Spatial

> Scene-wide space queries run on `core/spatial`: an `Octree` of entity AABBs owned by `Scene` (`world_size=1000.0`), per-mesh `BVH` structures with raycast queries, and frustum-culling batch helpers in `core/pyx/`. `Scene` exposes them as `spatial_query` / `spatial_raycast`.

[Home](../../../index.md) | [Section index](./_section.md)

## Overview

- `Octree` (`core/spatial/octp/tree.py`, re-exported from `core/spatial/__init__.py` with `AABB`, `OctreeNode`): constructor `(world_size=500.0, max_depth=8, max_objects=8)`; `Scene` owns one with `world_size=1000.0`. API: `insert(entity_id, aabb)` (auto-subdivides full leaves by center octant; re-inserts replace), `remove(entity_id)`, `update_xyz(entity_id, cx, cy, cz, rad)` (fast in-place move, `False` when the box left its node), `query(aabb)`, `query_point(point)`, `raycast(origin, direction, max_dist=100.0)` → `[(entity_id, t)]`. `OctreeNode` (`geometry.py`) implements the recursive primitives.
- `Scene` spatial glue (`core/ecs/scene_spatial.py` + `Scene` wrappers): dirty-transform flushing inserts/refreshes entity boxes (approx. ±2.5 units around the position), `rebuild_spatial()`, `spatial_query(aabb)`, `spatial_raycast(origin, direction, max_dist)`, `mark_spatial_dirty()`, and `entity.py` removes the id on entity removal.
- `BVH` (`core/spatial/bvhv/`): per-mesh acceleration over `vertices`/`indices` (`float32` node rows, `from_cache` rebuild, `flatten_for_gpu` / `flatten_for_gpu_stackless`, `node_depths`, `tri_indices`, `node_count`, `depth`). Queries (`query.py`): `intersect(ox..., vertices, indices)` → closest `t` (Möller–Trumbore + slab pre-test), `intersect_any` → bool, `build_bvh_arrays`. Building (`build.py`): SAH `_build` plus Morton LBVH `_build_lbvh` when mode is `"fast"` and triangle count > 4096; `lru.py` adds an LRU/disk cache (`_build_bvh`, `get_mesh_bvh`, `prebuild_mesh_bvh`). This is mesh-level (raycast/pick), not the entity octree.
- Culling (`core/pyx/`): `_culling.pyx` (`cpu_frustum_cull`, `cpu_frustum_cull_parallel`), `_render_utils.pyx` (`build_frustum_cull_inputs` sphere data for GPU culling), `_shadow_batch.pyx` (`frustum_cull_shadow_groups`).

## Properties

| structure | scope | key API |
|---|---|---|
| `Octree` | entities in the scene | `insert` / `remove` / `update_xyz` / `query` / `query_point` / `raycast` |
| `Scene` wrappers | scene convenience | `spatial_query` / `spatial_raycast` / `rebuild_spatial` / `mark_spatial_dirty` |
| `BVH` | one mesh | `intersect` / `intersect_any` / `flatten_for_gpu` / `get_mesh_bvh` |
| culling batches | render lists | `cpu_frustum_cull(_parallel)`, `build_frustum_cull_inputs`, `frustum_cull_shadow_groups` |

## Usage in Editor

Viewport picking and gizmo raycasts go through the spatial queries; mesh import can prebuild BVHs (`prebuild_mesh_bvh`) so the editor does not stall on first pick.

## Usage in Code

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

## Serialization

Spatial structures are never serialized; they rebuild from entity transforms (`rebuild_spatial`) and mesh assets on load.

## Related pages

- [ECS Overview](../ecs-overview.md)
- [Entity](../entity.md)
- [Math Helpers](./math-helpers.md)
- [Precision and 64-bit](./precision-64bit.md)

## Verified against

- `core/spatial/__init__.py` (re-exports `AABB`, `OctreeNode`, `Octree`)
- `core/spatial/octp/tree.py` (defaults, `insert`/`remove`/`update_xyz`/`query`/`query_point`/`raycast`)
- `core/spatial/octp/geometry.py` (`OctreeNode` primitives)
- `core/ecs/scene.py` (`_spatial = Octree(world_size=1000.0)`, `spatial_query`/`spatial_raycast` wrappers)
- `core/ecs/scene_spatial.py` (flush/insert/rebuild/query glue)
- `core/ecs/entity.py` (`_spatial.remove` on removal)
- `core/spatial/bvhv/bvh.py`, `query.py`, `build.py`, `lru.py` (mesh BVH + queries + cache)
- `core/pyx/_culling.pyx`, `_render_utils.pyx`, `_shadow_batch.pyx` (frustum-culling kernels), `_octree_batch.pyx` (`aabb_*_batch`), `_bvh_build.pyx` (`sah_compute_best_split`)
