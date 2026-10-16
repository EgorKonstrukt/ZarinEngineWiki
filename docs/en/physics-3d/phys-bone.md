# PhysBone

Bone-chain dynamics for hair, tails, capes, antennae — spring simulation along an existing bone chain. CPU Verlet, not a solver rigid body.

## Properties

| Group | Fields (defaults) |
|---|---|
| Transforms | `root_entity_id` `""`, `ignore_entity_ids` `[]`, `ignore_other_phys_bones` `False`, `endpoint_position` `(0,0,0)`, `multi_child_type` `ignore` (`ignore`/`first`/`average`) |
| Forces | `integration_type` `simplified` (`simplified`/`advanced`), `pull` `0.2`, `spring` `0.2`, `damping` `0.1`, `gravity` `0.0`, `gravity_falloff` `0.0`, `immobile_type` `all`, `immobile` `0.0` (each with `*_curve` default `[[0,1],[1,1]]`) |
| Limits | `limit_type` `none` (`none`/`angle`/`hinge`), `max_angle` `45.0` + curve, `hinge_axis` `(1,0,0)` |
| Collision | `radius` `0.11` + curve, `allow_collision` `True`, `collider_entity_ids` `[]` |
| Stretch | `stretch_motion` `0.0`, `max_stretch` `0.0`, `max_squish` `0.0` (+ curves) |
| Options | `parameter` `""`, `is_animated` `False`, `reset_when_disabled` `False`, `allow_grab` `True`, `show_gizmo` `True` |

## Usage in Editor

Add `PhysBone` (multiple allowed), set `Root Transform` to the chain root, tune pull/spring/damping, add `PhysBoneCollider` entities into `Colliders`.

## Usage in Code

```python
entity = scene.create_entity("Hair")
from core.components.physics.physbone.component import PhysBone

pb = PhysBone()
pb.root_entity_id = root_id
pb.pull = 0.2
pb.spring = 0.2
pb.gravity = 0.2
pb.allow_collision = True
pb.collider_entity_ids = [collider_entity_id]
entity.add_component(pb)
```

Chain rebuilds on `on_start` / `on_enable`; `on_disable`/`on_destroy` optionally `reset_to_rest()`.

## Fixed update notes

- Not stepped by `solver.step_simulation`; integrates per-frame on the component side (`_step_cython` pack when available). No `dt` coupling to `Engine._fixed_dt` in the verified path — runs in component update, colliders sampled via `world_center`/`world_capsule`/`world_plane`.

## Related

PhysBoneCollider, Joints (rigid alternative), Solver & Threading.

## Verified against

- `core/components/physics/physbone/component.py`
- `core/components/physics/physbone/params.py`
- `core/components/physics/physbone/types.py`
