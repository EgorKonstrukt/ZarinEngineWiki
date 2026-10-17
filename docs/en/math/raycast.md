# Raycast

Three layers, all Möller–Trumbore at the bottom with miss value `-1.0`:

| Layer | Entry | Returns |
|---|---|---|
| Triangle | `math_helpers.ray_triangle_intersect(...)` | `t` or `-1.0` (parallel guard `|det| < 1e-12`; barycentric reject; `t <= 0` rejected) |
| Mesh BVH | `BVH.intersect(...)` / `BVH.intersect_any(...)` | closest `t` / `bool` (slab pre-test `1e-30`, triangle test `1e-12`) |
| Scene octree | `Octree.raycast(origin, direction, max_dist=100.0)` | `[(entity_id, t)]` sorted by `t` |

Supporting casts: `ray_mesh_intersect` (closest over index triplets), `ray_aabb_intersect` (slab, `±1e30` sentinels, `1e-30` axis guard), `ray_sphere_intersect_batch` (struct-of-arrays batch), `AABB.intersects_ray(origin, direction, max_dist)` (`|d| < 1e-12` axis guard).

`Octree` defaults: `Octree(world_size=500.0, max_depth=8, max_objects=8)`; the `Scene`-owned instance uses `world_size=1000.0`. `Octree.update_xyz(entity_id, cx, cy, cz, rad)` is the fast in-place move — returns `False` when the box left its node. `AABB` defaults to `±0.5` (`min=(-0.5,-0.5,-0.5)`, `max=(0.5,0.5,0.5)`).

## Pick-object script

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

# Scene-level equivalent:
hit = scene.spatial_raycast(Vec3(0, 0, 5), Vec3(0, 0, -1), 100.0)

# Raw triangle / mesh level:
from core import math_helpers as mh
t = mh.ray_triangle_intersect(0, 0, 5, 0, 0, -1,
                              -1, -1, 0, 1, -1, 0, 0, 1, 0)
print(t)  # 5.0, or -1.0 on miss

from core.spatial.bvhv.bvh import get_mesh_bvh
bvh = get_mesh_bvh(vertices, indices)
t2 = bvh.intersect(0, 0, 5, 0, 0, -1, vertices, indices)
any_hit = bvh.intersect_any(0, 0, 5, 0, 0, -1, vertices, indices)
```

## Related

- [Math overview](overview.md)
- [Math Helpers](../core/advanced/math-helpers.md), [Spatial](../core/advanced/spatial.md)
- [Precision and 64-bit](../core/advanced/precision-64bit.md)

## Verified against

- `core/math_helpers.py:303-321` (`ray_triangle_intersect`), `:324-335` (`ray_mesh_intersect`), `:338-365` (`ray_aabb_intersect`), `:187-211` (sphere batch)
- `core/spatial/octp/tree.py:13-18` (defaults), `:20-45` (`update_xyz`), `:138-142` (`raycast`)
- `core/spatial/octp/geometry.py:13-19` (`AABB` defaults), `:58-81` (`intersects_ray`)
- `core/spatial/bvhv/query.py:86-150` (`intersect`), `:151-214` (`intersect_any`), `:56-83` (Möller `1e-12`)
