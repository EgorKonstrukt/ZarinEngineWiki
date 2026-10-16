# Mesh Collider

Collision from mesh geometry for cases primitives cannot fit: statues, vehicle hulls, concave scenery.

## Properties

| Field | Default | Notes |
|---|---|---|
| `mesh_path` | `""` | Resource filter `*.obj *.stl *.gltf *.glb` |
| `collision_mode` | `auto` | `auto` / `mesh` / `convex_hull` / `box` / `sphere` |
| `max_vertices` | `2000` | `0–100000`; convex-hull budget |
| `center` | `(0, 0, 0)` | Local offset |
| `layer` / `mask` | `0` / `0xFFFF` | Filtering |
| `is_trigger` | `False` | Overlap only |
| `physic_material` | `""` | `.zphysmat` path |
| `material_friction` / `material_bounciness` | `0.6` / `0.0` | Fallbacks |

## Usage in Editor

Assign `Mesh`, leave mode `auto` (static → exact `mesh`, dynamic → `convex_hull`). Keep hulls under `max_vertices`; distant meshes skip gizmos beyond 20 m in Play.

## Usage in Code

```python
entity = scene.create_entity("Statue")
from core.components.physics.meshcol.collider import MeshCollider

mc = MeshCollider()
mc.mesh_path = "assets/statue.obj"
mc.collision_mode = mc.collision_mode  # auto by default
mc.max_vertices = 2000
entity.add_component(mc)  # no Rigidbody = static scenery

def on_collision_enter(self, other_id, force=0.0):
    pass
```

Effective-mode rule (verified): `AUTO` → `MESH` when static, `CONVEX_HULL` when dynamic; explicit `MESH` on a dynamic body is forced to `CONVEX_HULL`. Dynamic bodies sharing one entity with a mesh downgrade the mesh to a box approximation (`downgrade_mesh_to_box`, warning logged); non-primitive extras become separate static/kinematic bodies.

## Fixed update notes

- Solver call: `create_rigid_body(shape_type="mesh", shape_params={"file", "collision_mode", "max_vertices", "scale", "center"})`.
- `dt` is `Engine._fixed_dt` (`0.02` default).

## Related

Rigidbody, Box collider (downgrade target), Terrain Collider, Physics Materials, Collision Layers.

## Verified against

- `core/components/physics/meshcol/collider.py` (`_effective_mode`, `_is_dynamic_body`)
- `core/components/physics/meshcol/mode.py` (`CollisionMode`)
- `core/physics/shapep/shapes.py` (`partition_compound_shapes`, `downgrade_mesh_to_box`)
