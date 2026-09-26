# Physics 3D Overview

The built-in 3D solver runs off the main thread: simulation steps in a background worker while the ECS thread renders, runs scripts and syncs transforms each frame. Gameplay code never waits on the solver; it reads last-step results and writes wishes (forces, velocities, teleports) that apply on the next step.

Pipeline per fixed step:

1. Register new entities (create solver bodies for fresh Rigidbody + collider pairs) and re-check shapes periodically for changed meshes and scales.
2. Sync ECS → physics: teleport dirty transforms straight into bodies, otherwise push accumulated forces/torques and dirty velocities.
3. Step the solver with `dt` (default `0.02` from `Engine._fixed_dt`).
4. Constrain 2D-flagged bodies (zero out-of-plane velocity so planar games never drift off-plane).
5. Sync physics → ECS: positions, rotations, velocities back onto Transforms and Rigidbody state; kinematic and dirty bodies are skipped (they are Transform-driven).
6. Dispatch collision events into `on_collision_enter/stay/exit` script callbacks with entity ids and impact forces.

Solvers plug through `IPhysicsSolver`: `culverin` (Jolt-based) by default, `pybullet` and `physx` as alternatives registered in `physics_solvers/registry.py`. Execution modes: `single` (in-process, lowest latency, required by CharacterController), `multi_threaded` (default, daemon solver process with command/result queues and shared buffers), `per_layer_process` (solver processes spawned on demand per collision layer).

Fixed-step plumbing: `Engine.tick_fixed_step` accumulates real time against `_fixed_dt`, runs plugin `pre_step`, `scene.fixed_update`, then the physics step — scripts always see a stable `dt` in `on_fixed_update` regardless of render framerate.

Body taxonomy: dynamic (positive-mass non-kinematic Rigidbody + collider — fully simulated), kinematic (driven by Transform, pushes dynamics, never pushed), static (collider alone — immovable scenery), trigger (reports overlaps, no contact response), character (solver move function), soft (deforming mesh), compound (merged primitives).

Subpages: Rigidbody, all six colliders, CharacterController, Joints, materials and layers, solver and threading internals, advanced bodies (buoyancy, soft body, PhysBone, motor).

First-room recipe: static floor (box collider, no Rigidbody), a few dynamic crates (Rigidbody mass 1–5 + box colliders), one directional light — press Play and the crates settle. Then add a CharacterController player and collision layers before content grows.
