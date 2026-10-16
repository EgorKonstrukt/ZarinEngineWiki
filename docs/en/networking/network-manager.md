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

Lifecycle API: `host()`, `connect()`, `disconnect()` (mirror the plugin, scoped to this scene via `NetworkManagerTransportMixin`); `on_awake` registers the instance and existing identities, `on_update` polls the shared `Transport` deque and dispatches by `MessageType`, `on_destroy` clears the singleton instance (does not disconnect — call `disconnect()` explicitly).

Gameplay API (verified in `netmgr/transport.py` + `netmgr/sync.py`):

- `spawn_prefab(prefab_path, pos=None, rot=None, owner_id=None)` / `spawn_entity(entity, owner_id=None)` — authoritative spawn control; spawned entities carry NetworkIdentity with prefab ids so remotes instantiate the same prefab.
- `despawn(net_id: int)` / `despawn_entity(entity: Entity)` — authoritative despawn by net id (server broadcasts `NET_DESPAWN`; client request goes to server).
- `send_rpc(net_id: int, method: str, args: dict | None = None, target: int | None = None)` — component-level remote call (`NET_RPC {"net_id", "method", "args", "target?"}`); server may `send_to(target)` unicast, otherwise broadcast.

Setup: exactly one active manager per scene (two managers fight over the transport). Tick rate trades bandwidth for freshness — 10–20 Hz for lobbies and strategy, 30–60 for shooters; thresholds on sync components matter more than raw ticks.

Troubleshooting: double spawns → two managers or auto-spawn plus manual spawn; despawn mismatch → despawn by id, never by local reference; RPC silent → handler registered after `poll` started, or name typo.

Related: Network Plugin (transport wiring), NetworkIdentity (spawned ids), NetworkSpawn (prefab pools), NetworkPlayer (per-peer data).
