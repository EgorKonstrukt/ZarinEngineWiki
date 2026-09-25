# Physics 3D Overview

The built-in 3D solver runs off the main thread: simulation steps in a background worker while the ECS thread renders, scripts and syncs transforms each frame.

Pipeline per fixed step:

1. Register new entities and re-check shapes periodically.
2. Sync ECS → physics (teleport dirty transforms, otherwise push accumulated forces/torques and dirty velocities).
3. Step the solver with `dt` (default `0.02`).
4. Constrain 2D-flagged bodies (zero out-of-plane velocity).
5. Sync physics → ECS (positions, rotations, velocities; kinematic and dirty bodies are skipped).
6. Dispatch collision events to `on_collision_enter/stay/exit` script callbacks.

Solvers: `culverin` (Jolt-based) by default, `pybullet` and `physx` as alternatives. Execution modes: `single` (in-process), `multi_threaded` (default, separate process), `per_layer_process` (processes on demand per layer).

Subpages: Rigidbody, colliders, CharacterController, joints, materials and layers, solver and threading, advanced bodies (buoyancy, soft body, PhysBone, motor).
