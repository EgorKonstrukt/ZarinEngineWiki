# Circle Collider 2D

Disc shape in plane: balls, wheels, coins, radial triggers and proximity sensors.

| Inspector group / field | Meaning |
|---|---|
| Collision: Layer / Collision Mask | Filtering |
| Shape: Offset | In-plane offset `[ox, oy]` |
| Shape: Radius | Disc radius |
| Is Trigger | Overlap events without contact response |
| Material: Physic Material | `.zphysmat` asset override |

Maps onto the solver as a sphere primitive. Rolls stably on Box Collider 2D ground with moderate friction; trigger discs make the best pickups (forgiving overlap vs exact rectangles).

Patterns: coins — small trigger discs with a spin animation and a pickup script; wheels — dynamic discs with Rigidbody2D, freeze nothing, moderate angular drag; bumpers — high-bounciness discs plus an impulse script on enter; enemy sight — large trigger disc driving AI state.

Stack discs for capsule-like enemies (two discs + a box) instead of wishing for a 2D capsule — the compound behaves identically.

Troubleshooting: rolls forever → zero drag/friction; will not roll → freeze rotation on; pickups missed at speed → tunneling, enlarge the disc or cap runner speed.

Related: Rigidbody 2D, Box Collider 2D, Sphere Collider (3D).
