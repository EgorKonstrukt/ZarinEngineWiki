# CharacterController

Kinematic-style first/third-person body driven by the solver move function — slope sliding, step climbing and pushable props without full rigidbody chaos.

Movement defaults: walk speed 260, run speed 440 (Shift), crouch speed 130, acceleration 10, air acceleration 10, friction 4, stop speed 80.

Jump and gravity: jump power 300, jump buffer 0.1 s, coyote time 0.1 s. Jumping works while both timers are alive (`can_jump = buffer > 0 and coyote > 0`, then `velocity.y = jump_power`) — forgiving platforming without extra code.

Shape: capsule radius 0.5, height 2.0 (total at least `radius * 2 + 0.1`), crouch height 1.2, step height 0.3, max slope 45°, push strength 30 against dynamic props.

Per fixed step: check grounded state, accelerate with friction on ground, apply gravity in air, call solver `move_character` with the desired velocity, write the resulting position back to the Transform, apply yaw rotation. Rotation beyond yaw (pitch/roll) stays script-side on the camera, never on the body.

Requirement: an in-process solver — the controller reads solver world state directly. In background-process modes the engine falls back to single-threaded physics with a console warning ("Use single-threaded physics mode"). Plan the mode before shipping character gameplay.

Wiring input: read `Input.GetAxis`/keys in `on_update` into a wish direction, consume it in `on_fixed_update` for movement, jump on `GetKeyDown` buffered by the controller itself. Keep camera and body yaw synchronized through one sensitivity value.

Troubleshooting: falls forever → no ground collider or mask excludes the player layer; sticks on stairs → step height below riser height, raise to 0.35+; slides on slopes → max slope above the slope angle or friction low; jitter on moving platforms → platform kinematic with matching velocity, parent instead of pushing.

Related: Capsule Collider (matching shape), Rigidbody (dynamic alternative), Solver and Threading (mode requirement), Input API.
