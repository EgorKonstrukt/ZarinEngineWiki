# Animation Overview

Skeletal and property animation: `Animation` plays one `AnimationClip`, `Animator` runs an `AnimatorController` state machine, IK solvers pose bones per frame.

## Properties / API

| Item | Source | Meaning |
|---|---|---|
| `AnimationClip` | `core/components/animation/animation_clip.py` | `name`, `length`, `loop`, `sample_rate` 60, `curves`, `rotation_curves`, `bone_curves`, `events`; `save`/`load` JSON, `save_unity` YAML |
| `Animation` | `core/components/animation/animation_component.py` | `clip` path, `play_on_start`, `speed`; `play`/`stop`/`pause` |
| `Animator` | `core/components/animation/animator_component.py` | `controller` path, `avatar`, `apply_root_motion`, `update_mode`; `set_float/int/bool/trigger`, `get_float/int/bool` |
| `AnimatorController` | `core/components/animation/animator_controller.py` | Layers, states, transitions, parameters (`float/int/bool/trigger`) |
| IK | `core/components/animation/ikp/ccd.py`, `ikp/fabrik.py` | `TwoBoneIK`, `FABRIKChain` (`_IKBase`: `target_entity_id`, `pole_entity_id`, `weight`) |
| Blendshapes | `core/assets/importer/anim.py`, `mesh.py` | `blendshape_names/indices/positions/normals` from `aiAnimMesh`, toggle `blendshapes` in `.import` |

## Usage in Editor

1. Add `Animation` for single-clip playback or `Animator` with a controller asset.
2. Assign clip/controller paths, set `speed`, `play_on_start` / default state.
3. Add `TwoBoneIK` or `FABRIKChain` to a bone entity, assign target/pole entities.
4. Imported FBX blendshapes arrive as `blendshape_*` arrays when `.import` `blendshapes: true`.

## Usage in Code

```python
anim = entity.get_component_by_name("Animation")
anim.play()
tz = entity.get_component_by_name("Animator")
tz.set_float("Speed", 1.0)
tz.set_trigger("Jump")
```

## Related

- Clip and Animator, IK and Blendshapes

## Verified against

- `core/components/animation/animation_clip.py`, `animation_component.py`, `animator_component.py`, `animator_controller.py`
- `core/components/animation/ikp/ccd.py`, `ikp/fabrik.py`
