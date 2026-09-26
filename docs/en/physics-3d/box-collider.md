# Box Collider

Axis-aligned box shape in local space. The workhorse for crates, walls, floors, triggers and buttons — cheapest shape in any solver, always prefer it when the form fits.

| Inspector group / field | Default | Meaning |
|---|---|---|
| Collision: Layer / Collision Mask | 0 / 0xFFFF | Who collides with whom |
| Shape: Center | origin | Local offset from the entity pivot |
| Shape: Size | one | Local extents, scaled by the Transform scale |
| Is Trigger | off | Overlap events without contact response |
| Material: Physic Material | — | `.zphysmat` asset override |
| Inline friction / bounciness | 0.6 / 0.0 | Used when no asset is assigned |

Several colliders may share one entity; box/sphere/capsule primitives merge into a single compound rigid body, so a table (top + 4 legs) simulates as one body with five shapes.

Workflows: floors — large thin box, static (no Rigidbody), friction 0.8+ so characters do not skate; crates — unit box with Rigidbody mass 2–5, freeze nothing; triggers — box with `is_trigger` on, scripted `on_collision_enter` for doors, pickups and checkpoints; buttons — thin trigger plate plus a visual mesh that dips on enter.

Compound trick: L-shaped stairs from 2–3 boxes beat any mesh collider on cost and stability. Keep boxes axis-aligned in local space; rotated owners are fine, skewed (non-uniformly sheared) parents distort the shape — fix the hierarchy instead.

Troubleshooting: falls through at speed → tunneling, cap velocity or lower fixed dt; trigger never fires → mask mismatch or both sides triggers with no listener script; wrong size → Transform scale forgotten, check `scaled_size` thinking (local × scale).

Related: Rigidbody, Physics Materials, Collision Layers, Mesh Collider (when boxes cannot fit), Box Collider 2D.
