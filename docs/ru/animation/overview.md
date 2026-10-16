# Обзор анимации

Скелетная и параметрическая анимация: `Animation` играет один `AnimationClip`, `Animator` крутит стейт-машину `AnimatorController`, IK-решалы ставят кости каждый кадр.

## Свойства / API

| Элемент | Источник | Смысл |
|---|---|---|
| `AnimationClip` | `core/components/animation/animation_clip.py` | `name`, `length`, `loop`, `sample_rate` 60, кривые, события; `save`/`load` JSON, `save_unity` YAML |
| `Animation` | `animation_component.py` | `clip`, `play_on_start`, `speed`; `play`/`stop`/`pause` |
| `Animator` | `animator_component.py` | `controller`, `avatar`, `apply_root_motion`; `set_float/int/bool/trigger` |
| `AnimatorController` | `animator_controller.py` | Слои, состояния, переходы, параметры |
| IK | `ikp/ccd.py`, `ikp/fabrik.py` | `TwoBoneIK`, `FABRIKChain` (`target_entity_id`, `pole_entity_id`, `weight`) |
| Блендшейпы | `core/assets/importer/anim.py` | `blendshape_names/indices/positions/normals` из `aiAnimMesh` |

## Использование в редакторе

1. `Animation` для одного клипа или `Animator` с контроллером.
2. Укажите пути клипа/контроллера, `speed`, `play_on_start` / стартовое состояние.
3. `TwoBoneIK` / `FABRIKChain` на кость, целите target/pole сущности.
4. Блендшейпы FBX приезжают при `.import` `blendshapes: true`.

## Использование в коде

```python
anim = entity.get_component_by_name("Animation")
anim.play()
tz = entity.get_component_by_name("Animator")
tz.set_float("Speed", 1.0)
tz.set_trigger("Jump")
```

## Связанные

- Клипы и Animator, IK и блендшейпы

## Проверено по

- `core/components/animation/animation_clip.py`, `animation_component.py`, `animator_component.py`, `animator_controller.py`, `ikp/`
