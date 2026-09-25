# Rigidbody 2D

Planar dynamics for side-scrollers and top-down games.

| Field | Meaning |
|---|---|
| Mass | Body weight |
| Drag / Angular Drag | Planar damping |
| Gravity Scale | Local gravity multiplier |
| Is Kinematic | Transform-driven, pushes others |
| Freeze Rotation | Lock in-plane spin |

Same script API as 3D (`velocity`, `add_force`, `add_torque`, `add_impulse`). The solver flags the body as 2D and zeroes out-of-plane velocity every step.

Related: Box Collider 2D, Circle Collider 2D, Rigidbody (3D).
