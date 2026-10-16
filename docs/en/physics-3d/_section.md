# Physics 3D

Rigid-body simulation: `Rigidbody` bodies + collider shapes stepped by a pluggable solver.

- [Physics 3D Overview](overview.md) — pipeline, modes, body taxonomy.
- [Rigidbody](rigidbody.md) — mass, forces, velocities, freeze axes.
- [Box Collider](box-collider.md) — cheapest primitive.
- [Sphere Collider](sphere-collider.md) — balls, rolls, radial triggers.
- [Capsule Collider](capsule-collider.md) — characters, pills.
- [Mesh Collider](mesh-collider.md) — exact/convex/box/sphere from geometry.
- [Terrain Collider](terrain-collider.md) — heightfield landscapes.
- [GS Volume Collider](gs-volume-collider.md) — Gaussian-splat volumes as boxes.
- [CharacterController](character-controller.md) — FPS/TPS kinematic body.
- [Joints](joints.md) — hinge/fixed/spring constraints.
- [Physics Materials](physics-materials.md) — friction/bounciness assets.
- [Collision Layers](collision-layers.md) — who collides with whom.
- [Solver & Threading](solver-threading.md) — modes, buffers, fixed step.
- [Buoyancy](buoyancy.md) — floating bodies on water volumes.
- [Soft Body](soft-body.md) — deformable meshes.
- [PhysBone](phys-bone.md) — bone-chain spring dynamics.
- [PhysBoneCollider](phys-bone-collider.md) — PhysBone deflectors.
- [Motor](motor.md) — hinge/slider constant drive.

## Verified against

- `core/components/physics/` (rigidbody, colliders, joint, motor, buoyancy, soft_body, physbone, pbcollider, character, gsvol, meshcol, terrain_collider)
- `core/physics/` (solver interface, scene, shapep, workers, process)
- `physics_solvers/registry.py`
