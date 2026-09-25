# Box Collider

Axis-aligned box shape in local space. The workhorse for crates, walls, floors and triggers.

| Inspector group / field | Default | Meaning |
|---|---|---|
| Collision: Layer / Collision Mask | 0 / 0xFFFF | Filtering |
| Shape: Center | origin | Local offset |
| Shape: Size | one | Local extents, scaled by the Transform |
| Is Trigger | off | Overlap events without contact response |
| Material: Physic Material | — | `.zphysmat` asset override |
| Inline friction / bounciness | 0.6 / 0.0 | Used without an asset |

Several colliders may share one entity; primitives merge into a compound rigid body.

Related: Rigidbody, Physics Materials, Collision Layers, Box Collider 2D.
