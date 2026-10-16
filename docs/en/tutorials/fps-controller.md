# Tutorial: FPS Controller

A playable first-person character in 6 steps: `CharacterController` component + linked camera, no custom movement code. Defaults below are the component's own (`core/components/physics/character/move.py`, `jump_slope.py`).

Key defaults: `walk_speed = 260.0`, `run_speed = 440.0` (while `Shift`), `crouch_speed = 130.0`, `jump_power = 300.0`, `jump_buffer_time = 0.1`, `coyote_time = 0.1`, `sensitivity = 5.0 / 5.0 / 5.0`, `cursor_lock_on_start = True`, `unlock_key = ESCAPE`, `camera_fov = 60.0`, `gravity = 800.0`, `capsule_radius = 0.5`, `capsule_height = 2.0`, `step_height = 0.3` (step offset), `max_slope = 45.0` (slope limit), `push_strength = 30.0`.

Hard-wired keys (`get_wish_dir` / `on_fixed_update`): `W/A/S/D` move, `Shift` run, `Space` jump (buffered + coyote time), `C` crouch, `Right mouse` aim zoom (`aim_fov = 45.0`), wheel zoom when `scroll_zoom`. Mouse look is internal (`sensitivity`, `invert_x/y`, `mouse_smoothing`); there are no pre-made `Horizontal`/`Vertical` axes involved.

Requirement: the controller needs the in-process solver — set physics `simulation_mode = 'single'` (otherwise it logs `physics world is not available in-process. Use single-threaded physics mode`).

## Steps

1. **Project physics to single mode.** In project config set `physics.simulation_mode = 'single'` (`PhysicsPlugin._get_physics_settings` reads the `physics.*` keys; `ensure_single_mode()` is the code fallback).
2. **Create the player.** Hierarchy → add entity, rename `Player`. `Add Component → CharacterController`. Leave `Capsule Radius 0.5 / Height 2.0 / Step Height 0.3 / Max Slope 45` for a standard humanoid.
3. **Create the camera.** Add entity `FPSCamera`, add `Camera` component, drag it onto `Player` (make it a child). The controller detects the child itself (`cam_tr._entity.parent is self._entity`) and places it at eye height each frame; or set `Camera` field (`camera_entity_id`) to link a free camera.
4. **Ground.** Add a large flat entity with a collider (static) at `y = 0` so `is_character_grounded` has something to stand on.
5. **Attach the probe script** below to `Player` for verification output.
6. **Press Play.** `WASD` moves, mouse looks (cursor locked; `Esc` unlocks), `Space` jumps, `C` crouches, `Shift` sprints. Tune `Walk Speed`, `Jump Power`, `Max Slope`, `Step Height` live in the Inspector.

## Full code — `fps_probe.py`

```python
class FpsProbe:
    def on_awake(self):
        self._cc = self._entity.get_component_by_name("CharacterController")
        self._was_grounded = None

    def on_update(self, dt):
        if self._cc is None:
            return
        grounded = self._cc.is_grounded
        if grounded != self._was_grounded:
            self._was_grounded = grounded
            Logger.info(f"grounded={grounded}")
        if Input.GetKeyDown(KeyCode.SPACE) and not grounded:
            v = self._cc.velocity  # Vec3, set from solver each fixed update
            Logger.info(f"jump, speed={v.length():.1f}")
```

Notes: `velocity`, `is_grounded`, `is_crouching`, `fixed_update_count` are read-only properties (`move.py`); `get_move_speed()` / `get_wish_dir()` expose the current intent; `set_ai_drive(dir, speed)` drives the character without keys. `resync_character()` rebuilds the solver capsule after changing radius/height at runtime.

## Verified against

`core/components/physics/character/move.py`, `core/components/physics/character/jump_slope.py`, `plugins/physics_plug/plugin.py`.
