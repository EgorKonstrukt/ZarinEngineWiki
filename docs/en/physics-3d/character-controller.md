# CharacterController

Kinematic-style first/third-person body driven by the solver move function — slope sliding, step climbing, pushable props without full-rigidbody chaos.

## Properties

| Field | Default | Notes |
|---|---|---|
| `walk_speed` / `run_speed` (Shift) / `crouch_speed` | `260.0` / `440.0` / `130.0` | Units/s |
| `acceleration` / `air_acceleration` | `10.0` / `10.0` | |
| `friction` / `stop_speed` | `4.0` / `80.0` | Ground friction model |
| `jump_power` | `300.0` | With `jump_buffer_time` / `coyote_time` `0.1` s each |
| `crouch_toggle` / `crouch_speed_mult` / `crouch_eye_offset` | `True` / `0.5` / `0.6` | |
| `sensitivity` / `sensitivity_x/y` | `5.0` / `5.0` / `5.0` | `invert_x/y` `False`, `mouse_smoothing` `0.0`, `smoothing_strength` `18.0` |
| `cursor_lock_on_start` / `unlock_key` | `True` / `ESCAPE` | |
| `camera_fov` / `near` / `far` | `60.0` / `0.05` / `1000.0` | `scroll_zoom` on, zoom `20–90`, aim `45` at speed `12` |
| `gravity` | `800.0` | Controller-local gravity (not world gravity) |
| `capsule_radius` / `capsule_height` / `crouch_height` | `0.5` / `2.0` / `1.2` | Total height clamped to `≥ radius*2 + 0.1` |
| `step_height` / `max_slope` / `push_strength` | `0.3` / `45.0` / `30.0` | Passed to `solver.create_character` |
| Read-only | `velocity`, `is_grounded`, `is_crouching`, `fixed_update_count` | `set_ai_drive(dir, speed)` / `ai_driving` for AI steering |

## Usage in Editor

Add `CharacterController` (no `Rigidbody` needed), link `Camera`, tune speeds/gravity/capsule. WASD + mouse by default, Shift runs.

## Usage in Code

```python
entity = scene.create_entity("Player")
from core.components.physics.character.jump_slope import CharacterController

cc = CharacterController()
cc.walk_speed = 260.0
cc.jump_power = 300.0
cc.capsule_radius = 0.5
cc.capsule_height = 2.0
entity.add_component(cc)

# AI drive instead of keyboard:
# cc.set_ai_drive(Vec3(0.0, 0.0, 1.0), 200.0)
# grounded = cc.is_grounded
```

## Fixed update notes

- Movement integrates in `on_fixed_update(dt)` with `dt = Engine._fixed_dt` (`0.02` default); mouse look runs in `on_update`.
- Requires `single` simulation mode: `on_start` resolves the solver via `_resolve_solver()` and falls back to `plugin.ensure_single_mode()`; without `culverin` (`_HAS_CULVERIN`) it returns early with no character.
- Character handle: `solver.create_character(pos, height, radius, step_height, max_slope)` + `move_character` / `is_character_grounded` / `destroy_character` (`on_disable` destroys it).

## Related

Rigidbody (kinematic alternative), Capsule Collider, Joints, Solver & Threading (mode requirement), Collision Layers.

## Verified against

- `core/components/physics/character/move.py` (defaults, `get_wish_dir`, `_accelerate`, `_apply_friction`)
- `core/components/physics/character/jump_slope.py` (inspector, `create_character`, `on_start`, `on_fixed_update`)
- `core/physics/physics_solver.py` (character API)
- `plugins/physics_plug/plugin.py` (`ensure_single_mode`)
