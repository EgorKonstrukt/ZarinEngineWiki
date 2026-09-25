# Sphere Collider

Rolling balls, head hitboxes, radial triggers.

| Inspector group / field | Default | Meaning |
|---|---|---|
| Collision: Layer / Collision Mask | 0 / 0xFFFF | Filtering |
| Shape: Center | origin | Local offset |
| Shape: Radius | 0.5 | Scaled by the largest Transform axis |
| Is Trigger | off | Overlap events without contact response |
| Material: Physic Material | — | `.zphysmat` asset override |
| Inline friction / bounciness | 0.6 / 0.0 | Used without an asset |

Recipe for a bouncy ball: bounciness ~0.8, friction ~0.2, own layer masked to world and player.

Related: Rigidbody, Capsule Collider, Physics Materials.
