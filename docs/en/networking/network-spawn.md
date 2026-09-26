# NetworkSpawn

Prefab pools and spawn points: what may spawn, where, and who triggers it.

| Inspector group / field | Meaning |
|---|---|
| Network Spawn: Spawnable Prefabs | Whitelist of prefab ids this spawner may instantiate |
| Spawn On Start | Populate on scene start |
| Spawn Radius | Random disc around the spawner |
| Randomize Rotation | Yaw variety per spawn |

API: `spawn(prefab_id)` at the spawner, `spawn_by_path(path)` for dynamic content, `spawn_player(peer)` wiring a connection to its avatar; `on_start` handles the initial population.

Whitelists are security: remotes can only request listed prefabs, so a hacked client cannot spawn admin objects. Spawn Radius decorrelates clustered spawns (mob packs, loot bursts); randomize rotation breaks visual repetition.

Patterns: player spawner (one per team base, spawn_player on join), wave spawner (timer script calling spawn per interval), loot spawner (spawn on enemy death event), vehicle pad (spawn on interact, despawn on abandon).

Authoritative flow: request → host validates prefab + cooldown + cap → `spawn_prefab` → NetworkIdentity assigns Net ID → remotes instantiate by prefab id. Never trust client-side spawn calls directly.

Troubleshooting: spawns nothing → prefab missing from whitelist; wrong prefab on remotes → prefab id mismatch across builds; spawn storms → no cooldown or cap, add both.

Related: NetworkManager (spawn_prefab/despawn), NetworkIdentity (ids + prefab ids), Prefabs (authoring), NetworkPlayer (player wiring).
