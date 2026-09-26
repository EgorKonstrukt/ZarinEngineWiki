# Rigidbody 2D

Planar dynamics for side-scrollers and top-down games — same API shape as 3D, constrained to the plane by the solver flag.

| Field | Meaning |
|---|---|
| Mass | Body weight; same mass-ratio guidance as 3D |
| Drag / Angular Drag | Planar damping |
| Gravity Scale | Local gravity multiplier (0 for top-down, 1 for platformers) |
| Is Kinematic | Transform-driven: moving platforms, doors |
| Freeze Rotation | Lock in-plane spin — almost always on for characters |

Same script API as 3D (`velocity`, `add_force`, `add_torque`, `add_impulse`). Every step the solver zeroes the out-of-plane components, so a top-down car never tips into the screen.

Patterns: player — dynamic with freeze rotation; moving platform — kinematic with scripted Transform motion (bodies riding it follow through contacts); projectile — dynamic, gravity scale 0, killed by lifetime script; top-down car — dynamic with high angular drag for arcade turn feel.

Troubleshooting: spins uncontrollably → freeze rotation off; floats in platformer → gravity scale 0 by mistake; platform drops riders → platform dynamic instead of kinematic.

Related: Box Collider 2D, Circle Collider 2D, Rigidbody (3D), Collision Layers.
