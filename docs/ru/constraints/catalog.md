# Каталог констрейнтов

Фолловеры трансформов: выполняются в `Scene.update` **до** обычных компонентов и IK (`query.py:135-205`). Все десять классов экспортированы из `core/components/constraints/__init__.py:7-23`. У каждого: `is_active`, `sources: [{entity_id, weight}]`, `add_source(entity, weight=1.0)`, `remove_source(index)`; оффсеты сохраняют текущую позу при ретаргете.

## PositionConstraint

Копирует взвешенную позицию источника по осям. Поля: `weight_x/y/z = 1.0`, внутренний `_offset = Vec3.zero()`; `set_offset_from_current_position()` хранит `position − target`, чтобы включение не дёргало объект.

## RotationConstraint

Копирует взвешенную ротацию по осям. Поля: `weight_x/y/z = 1.0`, `_offset_rotation = Quat.identity()`; `set_offset_from_current_rotation()`.

## ScaleConstraint

Копирует взвешенный скейл. Поля: `weight_x/y/z = 1.0`, `_offset_scale = Vec3(1,1,1)`; `set_offset_from_current_scale()`.

## ParentConstraint

Родительский в стиле Unity: позиция + ротация + скейл с поосевыми локами. Поля: `constrain_position_x/y/z`, `constrain_rotation_x/y/z`, `constrain_scale_x/y/z` (все `True`), `_position_offset`, `_rotation_offset`, `_scale_offset`; три хелпера `set_offset_from_current_*`.

## MoveTowardsConstraint

Движение к взвешенной цели с постоянной скоростью. Поля: `speed = 5.0`, `maintain_offset = False` (+ внутренний `_offset`); `set_offset_from_current_position()`.

## RotateTowardsConstraint

Доворот к цели с постоянной угловой скоростью. Поля: `speed = 90.0` град/с.

## ScaleToConstraint

Скейл к цели по отношению с клэмпом. Поля: `scale_factor = 1.0`, `min_scale = 0.5`, `max_scale = 2.0`.

## AimConstraint

Наведение локальной оси на цель со стабилизацией world-up. Поля: `aim_position_weight = 1.0`, `world_up_weight = 1.0`, `local_euler_axis_x = False`, `local_euler_axis_y = False`, `local_euler_axis_z = True`.

## LookAtConstraint

Look-at с блендом up-вектора. Поля: `look_at_weight = 1.0`, `world_up_weight = 1.0`.

## FollowTransformConstraint

Плавное следование с раздельными скоростями. Поля: `follow_position = True`, `follow_rotation = True`, `position_speed = 10.0`, `rotation_speed = 90.0`.

## Использование в коде

```python
from core.components.constraints.position_constraint import PositionConstraint

pc = PositionConstraint()
pc.add_source(leader_entity, weight=1.0)
pc.weight_x, pc.weight_y, pc.weight_z = 1.0, 1.0, 0.0  # лочим Z
entity.add_component(pc)
pc.set_offset_from_current_position()  # сохранить позу, без снэпа
pc.is_active = False  # пауза без удаления
pc.remove_source(0)
```

## Связанные

Animation IK Guide (IK идёт после констрейнтов), Transform.

## Проверено по

- `core/components/constraints/__init__.py:7-23` (10 экспортируемых имён)
- `core/components/constraints/position_constraint.py:37-62`, `rotation_constraint.py:39-63`, `scale_constraint.py:39-62`, `parent_constraint.py:49-101`, `move_towards_constraint.py:38-60`, `rotate_towards_constraint.py:37-47`, `scale_to_constraint.py:39-51`, `aim_constraint.py:43-57`, `look_at_constraint.py:38-49`, `follow_transform_constraint.py:41-56`
- `core/ecs/query.py:108-205` (констрейнты раньше others/IK)
