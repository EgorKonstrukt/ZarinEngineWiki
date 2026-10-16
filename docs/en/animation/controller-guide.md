# Animator Controller Guide

State machine asset: parameters drive conditional transitions between clip states. One controller holds parameters + layers; each layer holds states; each state holds transitions.

## Properties

| Item | Default | Notes |
|---|---|---|
| `AnimatorParameter` | `name="NewParam"`, type `FLOAT`, `default_float=0.0 / default_int=0 / default_bool=False` | Types: `float / int / bool / trigger` |
| `AnimatorCondition` | `parameter=""`, mode `IF`, `threshold=0.0` | Modes: `if / if_not / greater / less / equals / not_equal` |
| `AnimatorTransition.destination_state` | `""` | Target state name |
| `transition_duration` | `0.25` | Blend length; `has_fixed_duration=True` by default |
| `has_exit_time` / `exit_time` / `offset` | `False` / `0.0` / `0.0` | Exit-time gating + start offset |
| `mute` / `solo` | `False` / `False` | `mute` disables, `solo` isolates the transition |
| `AnimatorState` | `name="New State"`, `speed=1.0`, `motion_time=0.0`, `tag=""`, `x/y=0.0` | `clip` or `clip_path` |
| `AnimatorControllerLayer` | `name="Base Layer"`, `weight=1.0`, `blending_mode="override"` | `default_state=""` |

## Usage in Code

```python
from core.components.animation.animator_controller import (
    AnimatorController, AnimatorParameter, AnimatorParameterType,
    AnimatorState, AnimatorTransition, AnimatorCondition, AnimatorConditionMode,
)

ctrl = AnimatorController("Player")
ctrl.add_parameter(AnimatorParameter("Speed", AnimatorParameterType.FLOAT))
ctrl.add_parameter(AnimatorParameter("Jump", AnimatorParameterType.TRIGGER))

idle = ctrl.add_state(0, AnimatorState(name="Idle", clip_path="assets/idle.anim"))
run = ctrl.add_state(0, AnimatorState(name="Run", clip_path="assets/run.anim"))
ctrl.layers[0].default_state = "Idle"

idle.transitions.append(AnimatorTransition(
    destination_state="Run",
    conditions=[AnimatorCondition("Speed", AnimatorConditionMode.GREATER, 0.5)],
    transition_duration=0.25,
))
run.transitions.append(AnimatorTransition(
    destination_state="Idle",
    conditions=[AnimatorCondition("Speed", AnimatorConditionMode.LESS, 0.5)],
    transition_duration=0.25,
))
air = AnimatorTransition(destination_state="Run", transition_duration=0.1)
air.mute = False
air.solo = False  # set True to debug this transition in isolation
ctrl.save("assets/player.controller.json")
loaded = AnimatorController.load("assets/player.controller.json")
```

`IF` conditions check truthiness of `bool`/non-zero params; `GREATER/LESS/EQUALS/NOT_EQUAL` compare against `threshold`. `load()` also accepts Unity YAML (`%YAML` / `%TAG` / `--- !u!` header) via the Unity importer, otherwise JSON.

## Related

Animation Overview, Clip & Animator, IK Guide, Blendshapes Guide.

## Verified against

- `core/components/animation/animator_controller.py:15-90` (parameter/condition/transition types and defaults incl. `transition_duration=0.25`, `mute/solo`)
- `core/components/animation/animator_controller.py:93-228` (state/layer/controller API)
- `core/components/animation/animator_controller.py:271-290` (`load` JSON vs Unity YAML)
