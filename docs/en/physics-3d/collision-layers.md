# Collision Layers

Who collides with whom, independent of surface response. Every collider carries `layer` (default 0) and `mask` (default `0xFFFF`, collide with all layers); contact requires both sides to accept each other.

Filtering logic lives in `core/physics/collision_layers.py`. The `per_layer_process` execution mode can additionally isolate layers into on-demand solver processes for fully independent sub-simulations.

Layer plan for a typical action game:

- 0 World — static geometry, collides with everything gameplay.
- 1 Player — character and its hitboxes.
- 2 Enemy — bots; mute enemy-vs-enemy to save solver time on crowds.
- 3 Projectile — player and enemy shots; masked against world + characters, never against pickups.
- 4 Pickup — triggers only, masked to player.
- 5 Sensor — AI sight/hearing triggers, masked to player + enemy, no physical response.

Triggers (`is_trigger`) report overlaps through the same mask test without contact response — a sensor on the wrong layer is silent, not broken.

One-way platforms: platform collider on a layer masked out of the player mask, plus a thin top trigger that enables collision while the player overlaps from above — scripted in a few lines on `on_collision_enter/exit`.

Debugging: enable physics visualization (plugin) to color bodies by layer; silent pairs almost always mean mask mismatch, not missing components.

Related: Physics Materials, Solver and Threading (per-layer mode), all collider pages, Joints (connected bodies must share a colliding pair).
