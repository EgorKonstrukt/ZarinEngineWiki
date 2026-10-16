# Гайд по IK

Два солвера в `core/components/animation/ikp/`: аналитический двухкостный и итеративный FABRIK. Один IK-компонент на конечность: укажите цель — цепочка согнётся каждый кадр.

## Солверы

| Солвер | Поля | Примечания |
|---|---|---|
| `TwoBoneIK` | `root_bone / mid_bone / tip_bone` (пути), `target_entity_id` или `target_position`, `pole_entity_id` или `pole_position`, `bend_positive=True`, `stretch=0.0`, `weight=1.0` | Аналитика: корень доворачивается в сторону полюса (или стороны `bend_positive`), середина целится в цель. Пустые mid/tip — fallback на первого ребёнка |
| `FABRIKChain` | `root_bone`, `bones[]` (пути) или спуск по первым детям на `chain_length`, `iterations=8`, `tolerance=1e-4`, `bone_weights[]`, те же поля цели/полюса/веса | Итеративный проход туда-обратно; `_apply_fabrik_py` блендит по `weight` и `bone_weights` |

Общий `_IKBase`: `target_entity_id / target_position`, `pole_entity_id / pole_position`, `weight` (`ccd.py:44-50`); `_target_world()` предпочитает трансформ сущности сырой позиции.

## Порядок обновления

`Scene.update` партиционирует компоненты (`query.py:108-132`, `scene.py:353-354`): **constraints → others → IK** (`run_update`, `query.py:135-205`). IK идёт последним — поза костей считается после констрейнтов и обычных компонентов. Оба солвера сначала пробуют `core._ik.batch_update_ik`, при `ImportError` — чистый Python (`_solve_python`).

## Использование в коде

```python
# Нога: бедро -> колено -> стопа, полюс перед коленом
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
# Рука через FABRIK с явной цепочкой
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

## Связанные

Animation Overview, Animator Controller Guide, Constraints Catalog.

## Проверено по

- `core/components/animation/ikp/ccd.py:16-51` (`_IK_TYPES`, поля `_IKBase`)
- `core/components/animation/ikp/ccd.py:114-178` (поля `TwoBoneIK`, fallback костей, сначала `batch_update_ik`)
- `core/components/animation/ikp/fabrik.py:15-95` (поля `FABRIKChain`, резолв цепочки, fallback)
- `core/ecs/query.py:108-205` (партиция + порядок `constraints → others → IK`)
- `core/ecs/scene.py:353-354` (`_CONSTRAINT_TYPES`, `_IK_TYPES`)
