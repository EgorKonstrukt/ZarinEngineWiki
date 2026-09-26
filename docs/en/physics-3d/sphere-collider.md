# Sphere Collider

Perfect ball: rolling, bouncing, head hitboxes, radial triggers and proximity sensors.

| Inspector group / field | Default | Meaning |
|---|---|---|
| Collision: Layer / Collision Mask | 0 / 0xFFFF | Filtering |
| Shape: Center | origin | Local offset |
| Shape: Radius | 0.5 | Scaled by the largest Transform axis |
| Is Trigger | off | Overlap events without contact response |
| Material: Physic Material | — | `.zphysmat` asset override |
| Inline friction / bounciness | 0.6 / 0.0 | Used without an asset |

Rolling needs friction: a marble with friction 0.05 slides like ice, with 0.6 it grips and rolls. Bounce height comes from bounciness — 0 dead drop, 0.8 lively ball, never exactly 1 (energy gain jitters stacks).

Recipes: bouncy ball — radius 0.3, bounciness 0.8, friction 0.2, own layer masked to world + player; proximity sensor — large trigger sphere (radius 5–10) with a script counting enters/exits; head hitbox — small sphere on the head bone with a damage-reporting script.

Non-uniform parent scale picks the largest axis for the radius — a stretched parent makes a bigger ball than the mesh suggests. Keep visual and collision parents uniformly scaled.

Troubleshooting: rolls forever → angular drag 0 and no friction; will not roll → frozen rotation axes on the Rigidbody; trigger double-fires → enter/exit pairs on fast bodies, debounce in script.

Related: Rigidbody, Capsule Collider, Physics Materials, Circle Collider 2D.
