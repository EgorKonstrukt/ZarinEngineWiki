# IK Guide

Two solvers, both in `core/components/animation/ikp/`: analytic two-bone and iterative FABRIK. Attach one IK component per limb, point it at a target, and the chain bends each frame.

## Solvers

| Solver | Fields | Notes |
|---|---|---|
| `TwoBoneIK` | `root_bone / mid_bone / tip_bone` (paths), `target_entity_id` or `target_position`, `pole_entity_id` or `pole_position`, `bend_positive=True`, `stretch=0.0`, `weight=1.0` | Analytic: root rotates toward the pole side (or `bend_positive` side), mid aims at target. Missing mid/tip falls back to first child |
| `FABRIKChain` | `root_bone`, `bones[]` (paths) or `chain_length` walk down first children, `iterations=8`, `tolerance=1e-4`, `bone_weights[]`, same target/pole/weight fields | Iterative forward/backward pass; `_apply_fabrik_py` blends by `weight` and per-bone `bone_weights` |

Shared `_IKBase`: `target_entity_id / target_position`, `pole_entity_id / pole_position`, `weight` (`ccd.py:44-50`); `_target_world()` prefers the entity transform over the raw position.

## Update order

`Scene.update` partitions components (`query.py:108-132`, `scene.py:353-354`): **constraints → others → IK** (`run_update`, `query.py:135-205`). IK runs last so it poses bones after constraints and regular components. Both solvers first try `core._ik.batch_update_ik` and fall back to pure Python (`_solve_python`) on `ImportError`.

## Usage in Code

```python
# Leg: hip -> knee -> foot, pole in front of the knee
from core.components.animation.ikp.ccd import TwoBoneIK

ik = TwoBoneIK()
ik.root_bone = "Hips/LeftUpLeg"
ik.mid_bone = "Hips/LeftUpLeg/LeftLeg"
ik.tip_bone = "Hips/LeftUpLeg/LeftLeg/LeftFoot"
ik.target_entity_id = foot_target.id
ik.pole_entity_id = knee_pole.id
ik.bend_positive = True
ik.weight = 1.0
entity.add_component(ik)
```

```python
# Arm with FABRIK over an explicit chain
from core.components.animation.ikp.fabrik import FABRIKChain

fab = FABRIKChain()
fab.root_bone = "Spine/Shoulder"
fab.bones = ["Spine/Shoulder", "Spine/Shoulder/Elbow", "Spine/Shoulder/Elbow/Hand"]
fab.iterations = 8
fab.tolerance = 1e-4
fab.target_entity_id = hand_target.id
fab.weight = 1.0
entity.add_component(fab)
```

## Related

Animation Overview, Animator Controller Guide, Constraints Catalog.

## Verified against

- `core/components/animation/ikp/ccd.py:16-51` (`_IK_TYPES`, `_IKBase` fields)
- `core/components/animation/ikp/ccd.py:114-178` (`TwoBoneIK` fields, bone fallback, `batch_update_ik` first)
- `core/components/animation/ikp/fabrik.py:15-95` (`FABRIKChain` fields, chain resolution, fallback)
- `core/ecs/query.py:108-205` (partition + `constraints → others → IK` order)
- `core/ecs/scene.py:353-354` (`_CONSTRAINT_TYPES`, `_IK_TYPES`)
