# Constraints

Per-frame Transform followers: position/rotation/scale blend, look-at/aim, parenting and movement helpers. All are `ComponentRegistry.register` components with `is_active` gating.

## Properties / API

| Component | Key fields | Behavior (`on_update`) |
|---|---|---|
| `PositionConstraint` | `sources[{entity_id, weight}]`, `weight_x/y/z`, `is_active` | Weighted-average source position + stored offset, axis-blended |
| `RotationConstraint` | sources + axis weights | Weighted source rotation blend |
| `ScaleConstraint` | sources + axis weights | Weighted source scale blend |
| `ParentConstraint` | source entity | Copy source world transform (position+rotation) |
| `FollowTransformConstraint` | target + offsets | Hard-follow target Transform |
| `LookAtConstraint` | target, up | Orient `forward` toward target |
| `AimConstraint` | target, aim axis | Aim a local axis at target |
| `MoveTowardsConstraint` | target, speed, stop distance | Step position toward target |
| `RotateTowardsConstraint` | target, angular speed | Slerp rotation toward target |
| `ScaleToConstraint` | target scale, speed | Lerp scale toward value |

Common API: `add_source(entity, weight)`, `remove_source(index)`, `set_offset_from_current_position()`, `serialize()` / `deserialize()`.

## Usage in Editor

1. Add a constraint component to the follower entity.
2. Assign one or more source entities with weights; call `Set Offset From Current` (via `set_offset_from_current_position`) to keep the current gap.
3. Tune per-axis weights (`weight_x/y/z`) to lock axes; disable via `is_active`.

## Usage in Code

```python
c = entity.get_component_by_name("PositionConstraint")
c.add_source(leader_entity, weight=1.0)
c.set_offset_from_current_position()
c.weight_y = 0.0  # lock vertical

m = entity.get_component_by_name("MoveTowardsConstraint")
m.target_entity_id = goal.id
```

## Related

- Navigation (`NavAgent`), Animation (bone paths), Parenting/Transform

## Verified against

- `core/components/constraints/__init__.py`
- `core/components/constraints/position_constraint.py` and sibling `*_constraint.py` files
