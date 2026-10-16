# Tutorial: Network Session

A two-instance play session in 7 steps: `NetworkManager` hosts/spawns, `NetworkIdentity` + `NetworkTransform` + `NetworkPlayer` replicate each avatar. Defaults are the components' own (`netmgr/manager.py`, `network_identity.py`, `network_transform.py`, `network_player.py`).

`NetworkManager` defaults: `port = 7777`, `max_players = 16`, `server_name = "Zarin Server"`, `host_address = "127.0.0.1"`, `player_prefab = ""`, `auto_spawn_player = True`, `tick_rate = 20.0`. `NetworkTransform` defaults: `sync_position/rotation = True`, `sync_scale = False`, `authority = SERVER`, `send_rate = 20.0`, `interpolate = True`, `interp_delay = 0.1`, `teleport_threshold = 5.0`. `NetworkIdentity` starts `net_id = owner_id = -1`, `authority = SERVER`.

How spawning works (`manager.py:on_update`): on `PEER_JOINED`, if `is_server`, the manager syncs existing entities to the peer and — when `auto_spawn_player` and `player_prefab` are set — calls `spawn_prefab(prefab, pos=random ±2 m, owner_id=peer)`. `Transport: host(port, max_players)` binds `0.0.0.0`; `connect(address, port, name)` joins; `despawn(net_id)` removes everywhere.

## Steps

1. **Build the player prefab.** Entity `PlayerPrefab` with `NetworkIdentity` + `NetworkTransform` (+ `NetworkPlayer`, `player_name = "Player"`). Save as `assets/prefabs/player.zpep` (prefab path filter is `*.zpep`).
2. **Add the manager.** Entity `Net` with `NetworkManager`: set `Port = 7777`, `Player Prefab = assets/prefabs/player.zpep`, keep `Auto Spawn Player` on.
3. **Attach `net_host.py`** (below) to a `HostStarter` entity. Duplicate the project run or start a second editor instance with the same project.
4. **Host:** Play the first instance — `on_start` calls `host()` → log `NetworkManager hosting on 7777 max_players=16`.
5. **Join:** Play the second instance with `net_client.py` — `connect("127.0.0.1", 7777)`; host auto-spawns its avatar (`PEER_JOINED` → `spawn_prefab`).
6. **Move** the owner avatar: `NetworkTransform` broadcasts at `send_rate` when beyond `pos_threshold` (0.001) / `rot_threshold` (0.5°); remote copies interpolate with `interp_delay`.
7. **Verify:** host console shows `peer_count = 1`; both viewports show two avatars; stopping the client triggers the `LEAVE` path and despawns its entities.

## Full code

```python
# net_host.py -- attach in the hosting instance
class NetHost:
    port: int = 7777

    def on_start(self):
        from core.components.network.netmgr.manager import NetworkManager
        mgr = NetworkManager.get()
        if mgr is not None and mgr.host(self.port):
            Logger.info(f"hosting, local_id={mgr.local_id}")

    def on_update(self, dt):
        from core.components.network.netmgr.manager import NetworkManager
        mgr = NetworkManager.get()
        if mgr is not None and Input.GetKeyDown(KeyCode.H):
            Logger.info(f"server={mgr.is_server} peers={mgr.peer_count}")
```

```python
# net_client.py -- attach in the joining instance
class NetClient:
    address: str = "127.0.0.1"
    port: int = 7777

    def on_start(self):
        from core.components.network.netmgr.manager import NetworkManager
        mgr = NetworkManager.get()
        if mgr is not None and mgr.connect(self.address, self.port):
            Logger.info(f"connected to {self.address}:{self.port}")

    def on_update(self, dt):
        from core.components.network.netmgr.manager import NetworkManager
        mgr = NetworkManager.get()
        if mgr is not None and Input.GetKeyDown(KeyCode.H):
            Logger.info(f"connected={mgr.is_connected} peers={mgr.peer_count}")
```

`NetworkManager.get()` finds the scene's manager; `is_server / is_client / is_connected / local_id / peer_count` are live transport properties (`netmgr/transport.py`). Ownership: `NetworkIdentity.transfer_ownership(id)` (server only) / `request_ownership()` (client), `has_authority` follows `AuthorityMode`.

## Verified against

`core/components/network/netmgr/manager.py`, `core/components/network/netmgr/transport.py`, `core/components/network/netmgr/sync.py`, `core/components/network/network_identity.py`, `core/components/network/network_transform.py`, `core/components/network/network_player.py`, `plugins/network_plugin.py`.
