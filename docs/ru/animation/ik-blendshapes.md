# IK и блендшейпы

Два проверенных кодом IK-решала ставят цепочки костей к целям; дельты блендшейпов приезжают из Assimp-импортёра.

## Свойства / API

| Элемент | Источник | Смысл |
|---|---|---|
| `_IKBase.target_entity_id / target_position` | `ikp/ccd.py` | Сущность-цель или запасной `Vec3` |
| `_IKBase.pole_entity_id / pole_position` | `ikp/ccd.py` | Pole-подсказка |
| `_IKBase.weight` | `ikp/ccd.py` | Влияние 0–1 |
| `TwoBoneIK` | `ikp/ccd.py` | Решала двух костей |
| `FABRIKChain.root_bone / bones / chain_length` | `ikp/fabrik.py` | Цепочка; пустые `bones` идут по детям `root_bone` |
| `FABRIKChain.iterations / tolerance / bone_weights` | `ikp/fabrik.py` | По умолч. 8 / 1e-4 / веса |
| `blendshape_*` | `importer/anim.py`, `mesh.py` | Дельты каждого `aiAnimMesh` |
| `.import: blendshapes` | `importer/settings.py` | По умолч. `true` |

## Использование в редакторе

1. `TwoBoneIK` для конечностей, `FABRIKChain` для хвостов/позвоночников.
2. Задайте `root_bone` + `bones` (или `chain_length`), target/pole, `weight`, `iterations`.
3. Лица: импорт с `.import` `blendshapes: true`; меш несёт `blendshape_*` массивы.

## Использование в коде

```python
ik = entity.get_component_by_name("FABRIKChain")
ik.root_bone = "Hips/Spine"
ik.chain_length = 3
ik.iterations = 8
ik.target_entity_id = hand_target.id
```

## Связанные

- Обзор анимации, Клипы и Animator, Импорт моделей

## Проверено по

- `core/components/animation/ikp/ccd.py`, `ikp/fabrik.py`
- `core/assets/importer/anim.py`, `mesh.py`, `settings.py`
