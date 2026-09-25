# CharacterController

Kinematic-style first/third-person body driven by the solver move function, not by forces.

Movement defaults: walk speed 260, run speed 440 (Shift), crouch speed 130, acceleration 10, air acceleration 10, friction 4, stop speed 80.

Jump and gravity: jump power 300, jump buffer 0.1 s, coyote time 0.1 s (jumping works if both timers are alive: `velocity.y = jump_power`), gravity 800.

Shape: capsule radius 0.5, height 2.0 (total at least `radius * 2 + 0.1`), crouch height 1.2, step height 0.3, max slope 45°, push strength 30.

Per fixed step the controller: checks grounded state, accelerates with friction on ground, applies gravity in air, calls the solver `move_character` with the desired velocity, then writes the resulting position back to the Transform and applies yaw rotation.

Requirement: the controller needs an in-process solver (it reads solver world state directly). In background-process modes the engine falls back to single-threaded physics with a console warning.
