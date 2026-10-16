# Clip and Animator

`AnimationClip` stores curves and events; `Animation` plays one clip; `Animator` + `AnimatorController` blend states by parameters.

## Properties / API

| Field / Method | Default | Meaning |
|---|---|---|
| `AnimationClip.name / length / loop / sample_rate` | New Clip / 1.0 / true / 60 | Identity and timing |
| `add_curve(property_path, bone_path)` | — | `Curve` for float props, also indexed by `(bone, prop)` |
| `add_rotation_curve(property_path, bone_path)` | — | `QuaternionCurve` for rotations |
| `add_event(AnimationEvent)` | — | `time`, `function_name`, `string/float/int_parameter`, sorted by time |
| `evaluate(time)` / `evaluate_rotation` / `evaluate_all` | — | Sample props / quats / all bone curves |
| `Animation.clip / play_on_start / speed` | "" / true / 1.0 | Single-clip player; `play`/`stop`/`pause`, `on_start` auto-plays |
| `Animator.controller / avatar / apply_root_motion / update_mode` | "" / "" / false / normal | State-machine player |
| `set_float/int/bool/trigger`, `reset_trigger` | — | Drive transitions; trigger consumed on `IF` condition |
| `AnimatorState: name, clip_path, speed, transitions` | — | `transition_duration` 0.25, `has_exit_time`, `exit_time`, `mute/solo` |
| `AnimatorCondition: parameter, mode, threshold` | — | Modes `if/if_not/greater/less/equals/not_equal` |

## Usage in Editor

1. Create a clip asset (JSON `to_dict`/`from_dict`, Unity YAML via `save_unity`), set `length`, `loop`, keys.
2. Add `Animation`, point `clip` at the asset, enable `play_on_start`.
3. Or add `Animator`, point `controller` at `.animcontroller` JSON, set default state; parameters initialize from controller defs.

## Usage in Code

```python
from core.components.animation.animation_clip import AnimationClip, AnimationEvent

clip = AnimationClip(name="Bob", length=2.0)
c = clip.add_curve("position.y", bone_path="Hips")
c.add_key(0.0, 0.0)
c.add_key(1.0, 0.5)
clip.add_event(AnimationEvent(time=1.0, function_name="on_land"))
clip.save("assets/anim/bob.anim")

anim = entity.get_component_by_name("Animation")
anim.clip = "assets/anim/bob.anim"
anim.play()
boss = entity.get_component_by_name("Animator")
boss.set_float("Speed", 2.5)
boss.set_trigger("Attack")
```

## Related

- Animation Overview, IK and Blendshapes, Curve

## Verified against

- `core/components/animation/animation_clip.py`, `animation_component.py`
- `core/components/animation/animator_component.py`, `animator_controller.py`
