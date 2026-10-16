# Гайд по Animator Controller

Стейт-машина: параметры переключают условные переходы между состояниями-клипами. Один контроллер хранит параметры + слои; слой — состояния; состояние — переходы.

## Свойства

| Пункт | Дефолт | Примечания |
|---|---|---|
| `AnimatorParameter` | `name="NewParam"`, тип `FLOAT`, `default_float=0.0 / default_int=0 / default_bool=False` | Типы: `float / int / bool / trigger` |
| `AnimatorCondition` | `parameter=""`, режим `IF`, `threshold=0.0` | Режимы: `if / if_not / greater / less / equals / not_equal` |
| `AnimatorTransition.destination_state` | `""` | Имя целевого состояния |
| `transition_duration` | `0.25` | Длина бленда; `has_fixed_duration=True` по умолчанию |
| `has_exit_time` / `exit_time` / `offset` | `False` / `0.0` / `0.0` | Гейт по exit-time + стартовый оффсет |
| `mute` / `solo` | `False` / `False` | `mute` глушит, `solo` изолирует переход |
| `AnimatorState` | `name="New State"`, `speed=1.0`, `motion_time=0.0`, `tag=""`, `x/y=0.0` | `clip` или `clip_path` |
| `AnimatorControllerLayer` | `name="Base Layer"`, `weight=1.0`, `blending_mode="override"` | `default_state=""` |

## Использование в коде

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
air.solo = False  # True — отладить этот переход изолированно
ctrl.save("assets/player.controller.json")
loaded = AnimatorController.load("assets/player.controller.json")
```

Условия `IF` проверяют истинность `bool`/ненулевых параметров; `GREATER/LESS/EQUALS/NOT_EQUAL` сравнивают с `threshold`. `load()` принимает и Unity YAML (заголовок `%YAML` / `%TAG` / `--- !u!` — через Unity-импортёр), иначе JSON.

## Связанные

Animation Overview, Clip & Animator, IK Guide, Blendshapes Guide.

## Проверено по

- `core/components/animation/animator_controller.py:15-90` (типы параметров/условий/переходов и дефолты, включая `transition_duration=0.25`, `mute/solo`)
- `core/components/animation/animator_controller.py:93-228` (API состояний/слоёв/контроллера)
- `core/components/animation/animator_controller.py:271-290` (`load`: JSON vs Unity YAML)
