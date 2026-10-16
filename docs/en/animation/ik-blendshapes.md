# IK and Blendshapes

Two code-verified IK solvers pose bone chains toward targets; blendshape deltas arrive from the Assimp importer.

## Properties / API

| Item | Source | Meaning |
|---|---|---|
| `_IKBase.target_entity_id / target_position` | `ikp/ccd.py` | Target entity or fallback `Vec3` |
| `_IKBase.pole_entity_id / pole_position` | `ikp/ccd.py` | Pole (elbow/knee hint) entity or `Vec3` |
| `_IKBase.weight` | `ikp/ccd.py` | 0–1 solver influence |
| `TwoBoneIK` | `ikp/ccd.py` | Two-bone analytic/CCD solver (`_IK_TYPES` member) |
| `FABRIKChain.root_bone / bones / chain_length` | `ikp/fabrik.py` | Chain definition; empty `bones` walks children of `root_bone` |
| `FABRIKChain.iterations / tolerance / bone_weights` | `ikp/fabrik.py` | Defaults 8 / 1e-4 / per-bone weights |
| `blendshape_names / indices / positions / normals` | `core/assets/importer/ctypes_types.py` (`MeshImportData`), `anim.py:_finalize_blendshapes` | Per-`aiAnimMesh` deltas, capped sanity 10000 meshes |
| `.import: blendshapes` | `core/assets/importer/settings.py` | Default `true`; `skeleton_ctx.want_blend` gate |

## Usage in Editor

1. Add `TwoBoneIK` (limbs) or `FABRIKChain` (tails, spines) to the chain owner.
2. Set `root_bone` + `bones` (or `chain_length`), assign target/pole entities, tune `weight`, `iterations`.
3. For faces: import model with `.import` `blendshapes: true`; mesh carries `blendshape_*` arrays for the skinned renderer.

## Usage in Code

```python
ik = entity.get_component_by_name("FABRIKChain")
ik.root_bone = "Hips/Spine"
ik.chain_length = 3
ik.iterations = 8
ik.weight = 1.0
ik.target_entity_id = hand_target.id
```

## Related

- Animation Overview, Clip and Animator, Models via Assimp

## Verified against

- `core/components/animation/ikp/ccd.py`, `ikp/fabrik.py`
- `core/assets/importer/anim.py`, `mesh.py`, `settings.py`, `ctypes_types.py`
