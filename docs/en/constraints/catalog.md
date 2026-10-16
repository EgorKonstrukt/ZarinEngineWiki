# Constraints Catalog

Transform followers evaluated in `Scene.update` **before** regular components and IK (`query.py:135-205`). All ten classes are exported from `core/components/constraints/__init__.py:7-23`. Every class has `is_active`, `sources: [{entity_id, weight}]`, `add_source(entity, weight=1.0)`, `remove_source(index)`; offsets keep the current pose when retargeting.

## PositionConstraint

Copies the weighted source position per axis. Fields: `weight_x/y/z = 1.0`, internal `_offset = Vec3.zero()`; `set_offset_from_current_position()` stores `position − target` so enabling the constraint does not snap the object.

## RotationConstraint

Copies weighted source rotation per axis. Fields: `weight_x/y/z = 1.0`, `_offset_rotation = Quat.identity()`; `set_offset_from_current_rotation()`.

## ScaleConstraint

Copies weighted source scale. Fields: `weight_x/y/z = 1.0`, `_offset_scale = Vec3(1,1,1)`; `set_offset_from_current_scale()`.

## ParentConstraint

Unity-style parent: position + rotation + scale with per-axis locks. Fields: `constrain_position_x/y/z`, `constrain_rotation_x/y/z`, `constrain_scale_x/y/z` (all `True`), `_position_offset`, `_rotation_offset`, `_scale_offset`; three `set_offset_from_current_*` helpers.

## MoveTowardsConstraint

Moves toward the weighted target at constant speed. Fields: `speed = 5.0`, `maintain_offset = False` (+ internal `_offset`); `set_offset_from_current_position()`.

## RotateTowardsConstraint

Rotates toward the target at constant angular speed. Fields: `speed = 90.0` deg/s.

## ScaleToConstraint

Scales toward the target by ratio with clamping. Fields: `scale_factor = 1.0`, `min_scale = 0.5`, `max_scale = 2.0`.

## AimConstraint

Aims a local axis at the target with world-up stabilization. Fields: `aim_position_weight = 1.0`, `world_up_weight = 1.0`, `local_euler_axis_x = False`, `local_euler_axis_y = False`, `local_euler_axis_z = True`.

## LookAtConstraint

Look-at with up-vector blending. Fields: `look_at_weight = 1.0`, `world_up_weight = 1.0`.

## FollowTransformConstraint

Smooth follow with separate speeds. Fields: `follow_position = True`, `follow_rotation = True`, `position_speed = 10.0`, `rotation_speed = 90.0`.

## Usage in Code

```python
from core.components.constraints.position_constraint import PositionConstraint

pc = PositionConstraint()
pc.add_source(leader_entity, weight=1.0)
pc.weight_x, pc.weight_y, pc.weight_z = 1.0, 1.0, 0.0  # lock Z
entity.add_component(pc)
pc.set_offset_from_current_position()  # keep current pose, no snap
pc.is_active = False  # pause without removing
pc.remove_source(0)
```

## Related

Animation IK Guide (IK runs after constraints), Transform.

## Verified against

- `core/components/constraints/__init__.py:7-23` (the 10 exported names)
- `core/components/constraints/position_constraint.py:37-62`, `rotation_constraint.py:39-63`, `scale_constraint.py:39-62`, `parent_constraint.py:49-101`, `move_towards_constraint.py:38-60`, `rotate_towards_constraint.py:37-47`, `scale_to_constraint.py:39-51`, `aim_constraint.py:43-57`, `look_at_constraint.py:38-49`, `follow_transform_constraint.py:41-56`
- `core/ecs/query.py:108-205` (constraints run before others/IK)
