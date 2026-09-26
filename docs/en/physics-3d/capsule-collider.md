# Capsule Collider

Character bodies and pills: a cylinder with spherical caps that never snags on floor seams the way boxes do, and stays stable under rotation unlike raw cylinders.

| Inspector group / field | Default | Meaning |
|---|---|---|
| Collision: Layer / Collision Mask | 0 / 0xFFFF | Filtering |
| Shape: Center | origin | Local offset |
| Shape: Radius | 0.5 | Scaled by the largest axis |
| Shape: Height | 2.0 | Total height, scaled along the direction axis |
| Shape: Direction | 1 (Y) | 0 = X, 1 = Y, 2 = Z |
| Is Trigger | off | Overlap events without contact response |
| Material: Physic Material | — | `.zphysmat` asset override |

Matches the CharacterController capsule (radius 0.5, height 2.0) when defaults are kept — use the same numbers for AI bodies so players and bots collide identically.

Orientation guide: Y for standing characters, X/Z for lying or rolling bodies (logs, barrels on their side). Height includes the caps; a height below `radius * 2` degenerates toward a sphere — the solver clamps, but the gizmo shows the truth.

Enemies with lunges and dashes stay upright by freezing rotation axes on the Rigidbody while keeping the capsule — tips without toppling.

Troubleshooting: catches on steps → radius too large for the tread depth or step height (controller) too low; floats above ground → center offset lifting the caps; spins like a log → freeze rotation axes.

Related: CharacterController, Sphere Collider, Rigidbody.
