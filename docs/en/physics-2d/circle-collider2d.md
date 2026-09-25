# Circle Collider 2D

Disc shape in plane: balls, wheels, radial triggers.

| Inspector group / field | Meaning |
|---|---|
| Collision: Layer / Collision Mask | Filtering |
| Shape: Offset | In-plane offset `[ox, oy]` |
| Shape: Radius | Disc radius |
| Is Trigger | Overlap events without contact response |
| Material: Physic Material | `.zphysmat` asset override |

Maps onto the solver as a sphere primitive. Rolls stably on Box Collider 2D ground with low friction.

Related: Rigidbody 2D, Box Collider 2D, Sphere Collider (3D).
