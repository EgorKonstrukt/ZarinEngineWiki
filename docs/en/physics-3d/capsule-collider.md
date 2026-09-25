# Capsule Collider

Character bodies and pills: a cylinder with spherical caps, stable under rotation.

| Inspector group / field | Default | Meaning |
|---|---|---|
| Collision: Layer / Collision Mask | 0 / 0xFFFF | Filtering |
| Shape: Center | origin | Local offset |
| Shape: Radius | 0.5 | Scaled by the largest axis |
| Shape: Height | 2.0 | Scaled along the direction axis |
| Shape: Direction | 1 (Y) | 0 = X, 1 = Y, 2 = Z |
| Is Trigger | off | Overlap events without contact response |
| Material: Physic Material | — | `.zphysmat` asset override |

Matches the CharacterController capsule (radius 0.5, height 2.0) when defaults are kept.

Related: CharacterController, Sphere Collider, Rigidbody.
