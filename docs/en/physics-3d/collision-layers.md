# Collision Layers

Who collides with whom. Every collider carries `layer` (default 0) and `mask` (default `0xFFFF`, collide with all layers); the pair must match on both sides for contact.

Filtering logic lives in `core/physics/collision_layers.py`. The `per_layer_process` execution mode can additionally isolate layers into on-demand solver processes.

Patterns: separate world, player, enemy and projectile layers; mute enemy-vs-enemy to save solver time; put one-way platforms on a layer masked out of the player mask from below via triggers.

Related: Physics Materials, Solver and Threading, all collider pages.
