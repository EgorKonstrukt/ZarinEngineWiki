# Soft Body

Deformable mesh simulation for cloth, jelly, squashy props — vertices simulated by the solver, render mesh follows.

## Properties

| Field | Default | Notes |
|---|---|---|
| `layer` / `mask` | `0` / `0xFFFF` | Filtering |
| `mass` | `1.0` | Inspector `0.01–10000` |
| `stiffness` | `0.85` | Slider `0–1`; `compliance = (1-stiffness) * 0.01` |
| `bend_mode` | `distance` | `none` / `distance` / `dihedral` |
| `pressure` | `0.0` | Volume preservation |
| `damping` | `0.1` | `0–10` |
| `iterations` | `10` | `1–50` solver iterations |
| `gravity_scale` (`gravity_factor`) | `1.0` | `0–5` |
| `vertex_radius` | `0.05` | `0.001–1` |
| `max_velocity` | `500.0` | `1–5000` |
| `pin_mode` | `none` | `none` / `top` / `bottom` band `pin_fraction` |
| `pin_fraction` | `0.1` | `0–0.5` |
| `max_vertices` | `3000` | `0–30000`; voxel decimation applies when the mesh exceeds the budget (and `max_vertices ≥ 4`) |
| `double_sided` | `True` | |
| `update_com` | `True` | Drive entity Transform from center of mass |
| `recompute_normals` | `True` | |
| `material_friction` / `material_bounciness` | `0.2` / `0.0` | No asset path on this component |
| Caps | `16` bodies, `32768` verts each | `SoftSharedBuffer` |

## Usage in Editor

Add `MeshFilter` (mesh source) + `SoftBody`. Do **not** add `Rigidbody` — SoftBody owns the entity (warning logged, rigid body skipped). Pin `top` for curtains, `bottom` for tablecloths.

## Usage in Code

```python
entity = scene.create_entity("Cloth")
from core.components.physics.soft_body import SoftBody

sb = SoftBody()
sb.mass = 1.0
sb.stiffness = 0.85
sb.iterations = 10
sb.pin_mode = sb.pin_mode  # none by default
entity.add_component(sb)  # needs MeshFilter with mesh_path on same entity
```

Solver entry: `create_soft_body(entity_id, vertices, indices, position, rotation, mass, compliance, bend_mode, pressure, damping, iterations, gravity_factor, friction, restitution, vertex_radius, max_velocity, max_vertices, pin_mode, pin_fraction, double_sided, update_com, layer, mask)`. Teleports rebuild the soft body; COM sync skipped while pinned.

## Fixed update notes

- Bodies build in `_create_entity_soft` from `MeshFilter.mesh_path` via `load_collision_geometry`; results sync in `_sync_soft_bodies` / `_sync_soft_mesh` after `step_simulation(dt)` with `dt = Engine._fixed_dt` (`0.02` default).
- Multi-soft overlap separated by AABB impulse (`_separate_soft_bodies`) for `≥2` soft bodies.

## Related

Rigidbody (do not combine), Mesh Collider, Solver & Threading (soft buffers), Collision Layers.

## Verified against

- `core/components/physics/soft_body.py`
- `core/physics/scene/colliders.py` (`_build_entity_soft`, `_soft_sig`)
- `core/physics/scene/events.py` (`create_soft_body_in_solver`)
- `physics_solvers/culverin_solver/solverp/integrate.py` (`create_soft_body`)
- `core/physics/soft_shared_buffer.py`
