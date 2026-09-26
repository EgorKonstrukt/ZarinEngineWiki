# Solver and Threading

Where simulation actually runs, and how data crosses the thread/process boundary without gameplay code ever blocking.

Execution modes:

- `single` — `PhysicsScene` + solver in the game process. Lowest latency, simplest debugging, required by `CharacterController` (it reads solver world state directly).
- `multi_threaded` (default) — a daemon `multiprocessing.Process` owns the solver; command and result queues plus `SharedPhysicsBuffer` / `SoftSharedBuffer` carry transforms, velocities, forces and collision events.
- `per_layer_process` — solver processes spawned on demand per collision layer for isolated sub-simulations.

Background step, in order:

1. Pending `step` commands coalesce into one `dt` — hitches never spiral into catch-up death.
2. The worker applies teleports (`set_body_transform`) or velocities/forces/torques from shared memory into bodies.
3. `solver.step_simulation(dt)` integrates; 2D-flagged bodies get out-of-plane velocity zeroed right after.
4. Positions, rotations and velocities write back with a version bump; soft-body vertices, centers of mass and velocities go to the soft buffer.
5. Collision events map solver body ids to entity ids and land in the result queue for script dispatch with impact forces.

Per-body shared flags: 1 = active, 2 = velocity dirty, 4 = teleport, 8 = 2D body. The in-process path mirrors the same order without serialization: register → ECS-to-physics → step → constrain 2D → physics-to-ECS → soft sync → events.

Sync details that matter: ECS→physics pushes only when the Transform is dirty or forces accumulated — untouched sleeping bodies cost nothing; physics→ECS skips kinematic and dirty bodies (Transform-driven, never overwritten); force accumulators clear after every push, so `add_force` must be called each fixed step for continuous thrust.

Fixed-step plumbing: `Engine.tick_fixed_step` accumulates real time against `_fixed_dt = 0.02`, runs plugin `pre_step`, `scene.fixed_update`, then the physics step. Render hitches do not change physics `dt` — determinism holds.

Choosing a mode: prototype in default multi-threaded; switch to single when using CharacterController or debugging solver behavior; per-layer for minigame arenas that must never interact.

Troubleshooting: laggy response → background process starved, check CPU or switch single; teleport ignored → body asleep or flag race, wake via velocity write; stale reads in scripts → you are reading between steps, which is by design — act in `on_fixed_update`.

Related: Physics Overview, Rigidbody, Collision Layers, CharacterController, Profiler (physics_ms).
