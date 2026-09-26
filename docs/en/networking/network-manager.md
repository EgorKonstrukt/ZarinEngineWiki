# NetworkManager

Session component: one per scene, owns hosting, joining, player spawns, despawns and RPC fan-out at entity level.

| Inspector group / field | Meaning |
|---|---|
| Network Manager: Port | Listen/connect port, default 7777 |
| Max Players | Room cap |
| Server Name | Lobby display name |
| Host Address | Last/preset join target |
| Player Prefab | Spawned per connection |
| Auto Spawn Player | Spawn on join without script |
| Tick Rate | Sync ticks per second |
| Show Debug | Overlay session state |

Lifecycle API: `host()`, `connect()`, `disconnect()` (mirror the plugin, scoped to this scene); `on_awake` prepares, `on_update` pumps, `on_destroy` disconnects cleanly.

Gameplay API:

- `spawn_prefab(path_or_id)` / `despawn(entity)` / `despawn_entity(id)` — authoritative spawn control; spawned entities carry NetworkIdentity with prefab ids so remotes instantiate the same prefab.
- `send_rpc(name, payload)` — component-level remote call reaching all peers' handlers.

Setup: exactly one active manager per scene (two managers fight over the transport). Tick rate trades bandwidth for freshness — 10–20 Hz for lobbies and strategy, 30–60 for shooters; thresholds on sync components matter more than raw ticks.

Troubleshooting: double spawns → two managers or auto-spawn plus manual spawn; despawn mismatch → despawn by id, never by local reference; RPC silent → handler registered after `poll` started, or name typo.

Related: Network Plugin (transport wiring), NetworkIdentity (spawned ids), NetworkSpawn (prefab pools), NetworkPlayer (per-peer data).
