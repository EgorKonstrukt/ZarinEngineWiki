# Solver and Threading

Execution modes:

- `single` — `PhysicsScene` + solver in the game process. Lowest latency, required by `CharacterController`.
- `multi_threaded` (default) — a daemon `multiprocessing.Process` owns the solver; command and result queues plus shared buffers (`SharedPhysicsBuffer`, `SoftSharedBuffer`) carry transforms, velocities, forces and collision events.
- `per_layer_process` — solver processes spawned on demand per collision layer.

Background step:

1. Pending `step` commands coalesce into one `dt` (no spiral of death on hitches).
2. The worker applies teleports (`set_body_transform`) or velocities/forces/torques from shared memory.
3. `solver.step_simulation(dt)` runs; 2D-flagged bodies get out-of-plane velocity zeroed.
4. Positions, rotations and velocities are written back with a version bump; soft-body vertices, center of mass and velocities go to the soft buffer.
5. Collision events map solver body ids to entity ids and land in the result queue for script dispatch.

Per-body shared flags: 1 = active, 2 = velocity dirty, 4 = teleport, 8 = 2D body. The in-process path mirrors the same order without serialization: register → ECS-to-physics → step → constrain 2D → physics-to-ECS → soft sync → events.

Fixed-step plumbing: `Engine.tick_fixed_step` accumulates real time against `_fixed_dt = 0.02`, runs plugin `pre_step`, `scene.fixed_update`, then the physics step — scripts see a stable `dt` in `on_fixed_update`.
