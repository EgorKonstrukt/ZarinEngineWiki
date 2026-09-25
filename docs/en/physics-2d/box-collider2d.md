# Box Collider 2D

Rectangle shape in plane: platforms, walls, hitboxes.

| Inspector group / field | Meaning |
|---|---|
| Collision: Layer / Collision Mask | Filtering |
| Shape: Offset | In-plane offset `[ox, oy]` |
| Shape: Size | In-plane extents |
| Is Trigger | Overlap events without contact response |
| Material: Physic Material | `.zphysmat` asset override |

Maps onto the solver as a flat box `[sx, sy, 1.0]`. Combine with Rigidbody2D; never mix 2D and 3D components on one entity.

Related: Rigidbody 2D, Circle Collider 2D, Box Collider (3D).
