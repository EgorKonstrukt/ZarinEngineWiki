# Клипы, Animator, контроллеры

`AnimationClip` хранит кривые и события; `Animation` играет один клип; `Animator` + `AnimatorController` блендят состояния по параметрам.

## Свойства / API

| Поле / метод | Умолчание | Смысл |
|---|---|---|
| `AnimationClip.name / length / loop / sample_rate` | New Clip / 1.0 / true / 60 | Идентичность и тайминг |
| `add_curve / add_rotation_curve` | — | `Curve` / `QuaternionCurve` по `(bone, prop)` |
| `add_event(AnimationEvent)` | — | `time`, `function_name`, параметры; сортировка по времени |
| `evaluate / evaluate_rotation / evaluate_all` | — | Сэмплирование |
| `Animation.clip / play_on_start / speed` | "" / true / 1.0 | Плеер одного клипа |
| `Animator.controller / avatar / apply_root_motion` | "" / "" / false | Плеер стейт-машины |
| `set_float/int/bool/trigger`, `reset_trigger` | — | Триггер `IF` съедается при переходе |
| `AnimatorState / AnimatorTransition / AnimatorCondition` | duration 0.25 | Состояния, `has_exit_time`, режимы `if/if_not/greater/less/equals/not_equal` |

## Использование в редакторе

1. Создайте клип (JSON или Unity YAML через `save_unity`), задайте `length`, `loop`, ключи.
2. `Animation` с путём `clip` и `play_on_start`.
3. Либо `Animator` с `.animcontroller`, стартовое состояние и параметры.

## Использование в коде

```python
from core.components.animation.animation_clip import AnimationClip, AnimationEvent

clip = AnimationClip(name="Bob", length=2.0)
c = clip.add_curve("position.y", bone_path="Hips")
c.add_key(0.0, 0.0)
c.add_key(1.0, 0.5)
clip.add_event(AnimationEvent(time=1.0, function_name="on_land"))
clip.save("assets/anim/bob.anim")
boss = entity.get_component_by_name("Animator")
boss.set_float("Speed", 2.5)
boss.set_trigger("Attack")
```

## Связанные

- Обзор анимации, IK и блендшейпы, Curve

## Проверено по

- `core/components/animation/animation_clip.py`, `animation_component.py`, `animator_component.py`, `animator_controller.py`
