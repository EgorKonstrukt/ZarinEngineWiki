# Network Scripting

Multiplayer from gameplay code: RPCs, message subscriptions, prefab spawn/despawn, authority, and send rates.

## Concepts (as the code defines them)

- `NetworkManager` component: transport control + spawn/despawn + RPC send. Singleton via `NetworkManager.get()`.
- `NetworkIdentity`: per-entity `net_id`/`owner_id`/`prefab_id` + `AuthorityMode`: `SERVER`, `OWNER`, `SHARED`.
- Sync components: `NetworkTransform` (authority `SERVER`/`OWNER`, `send_rate` default `20.0`), `NetworkVariables` (authority + `send_rate` default `10.0`), plus `NetworkRigidbody`/`NetworkAnimator` (same `send_rate` pattern). `NetworkManager` itself has **`tick_rate`** (default `20.0`), not `send_rate`.
- RPC handler lookup on the receiving entity: method decorated with `@server_rpc`/`@client_rpc`/`@target_rpc` called as `fn(args_dict)` → else `handle_rpc(method, args, sender)` → else `rpc_<method>(**args)`.
- Plugin bus: `NetworkPlugin.on_message(msg_type, handler)` receives `NetworkMessage(msg_type, payload, sender_id)`; `"*"` subscribes to everything.

## NetworkManager API

| Signature | Behavior |
|---|---|
| `NetworkManager.get() -> NetworkManager \| None` | Cached `_instance`, else scans the active scene for the component. |
| `mgr.host(port=None, max_players=None) -> bool` | Hosts on `0.0.0.0`; resets net-id tables, registers existing identities. |
| `mgr.connect(address=None, port=None, name="Player") -> bool` | Connects as client; clears net tables. |
| `mgr.disconnect()` | Drops transport, clears tables. |
| `mgr.is_server` / `is_client` / `is_connected` / `local_id` / `peer_count` | Transport-backed read-only properties. |
| `mgr.send_rpc(net_id, method, args=None, target=None)` | Broadcast when server (or no target); `send_to(target)` when server + target set; plain `send` from a client. Payload: `{net_id, method, args, t}`. |
| `mgr.spawn_entity(entity, owner_id=None) -> int` | Server: assigns net id, stamps identity, broadcasts `NET_SPAWN`. Client: sends `NET_SPAWN_REQUEST`, returns `-1`. |
| `mgr.spawn_prefab(prefab_path, pos=None, rot=None, owner_id=None)` | Server: `Prefab.load` for `.zpep` paths, sets `Transform` from `pos` (`Vec3`/list) and `rot` (`Quat`/list), stamps `prefab_id`, broadcasts. Client: forwards a spawn request to the server. Returns the entity (server) or `None` (client). |
| `mgr.despawn(net_id)` / `mgr.despawn_entity(entity)` | Server broadcasts `NET_DESPAWN` then removes; client forwards and returns. Non-owner clients cannot despawn others' entities. |

Identity helpers: `ident.is_spawned` (`net_id >= 0`), `ident.is_owner`, `ident.has_authority` (SHARED ⇒ always; OWNER ⇒ `is_owner`; SERVER ⇒ transport `is_server`), `transfer_ownership(new_owner)` (server only), `request_ownership()` (client→server).

`NetworkVariables`: `set_var(name, value)` (marks dirty, sends on change), `get_var(name, default)`, `set_vars(dict)`, `has_var`, `remove_var`, `all_vars()`; `sync_on_change` + `send_rate` gate the `on_update` flush.

## Score-sync example (full, runnable)

Server-authoritative score: clients request a bump via RPC, the server updates a `NetworkVariables` entry that replicates to everyone:

```python
from core.network.rpc import server_rpc


class ScoreSync:
    score_key: str = "score"

    def on_start(self):
        from core.components.network.netmgr.manager import NetworkManager
        self.mgr = NetworkManager.get()
        ident = self._entity.get_component_by_name("NetworkIdentity")
        self.net_id = ident.net_id if ident is not None else -1
        self.vars = self._entity.get_component_by_name("NetworkVariables")
        # subscribe to a lightweight bus message as well:
        try:
            from core.engine.eng import Engine
            plug = Engine.instance().plugin_manager.get_plugin("NetworkPlugin")
            if plug is not None:
                plug.on_message("score_flash", self._on_flash)
        except Exception:
            pass

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.P):
            if self.mgr is not None and self.net_id >= 0:
                self.mgr.send_rpc(self.net_id, "add_point", {"by": 1})

    @server_rpc
    def add_point(self, args):
        if self.vars is None:
            return
        cur = int(self.vars.get_var(self.score_key, 0))
        self.vars.set_var(self.score_key, cur + int(args.get("by", 1)))

    def _on_flash(self, msg):
        Logger.info(f"flash from {msg.sender_id}: {msg.payload}")

    def current_score(self):
        return int(self.vars.get_var(self.score_key, 0)) if self.vars is not None else 0
```

Setup: entity carries `NetworkIdentity` (authority `SERVER`) + `NetworkVariables` + this script. `send_rpc` routes by role automatically; `@server_rpc` executes on the server copy via `invoke_rpc`.

Spawning a networked prefab from gameplay:

```python
class NetSpawner:
    prefab_path: str = "assets/prefabs/player.zpep"

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.N) and self.mgr.is_server:
            ent = self.mgr.spawn_prefab(self.prefab_path, pos=Vec3(0.0, 1.0, 0.0))
            Logger.info(f"spawned: {ent.name if ent else None}")
```

## Related

- `entity-api.md` — the spawned entities are normal entities with identities attached.
- `prefab-scripting.md` — local (non-networked) prefab spawning.
- `timers-coroutines.md` — throttle `send_rpc` calls with a cooldown timer, not per frame.

## Verified against

- `core/components/network/netmgr/manager.py:22-65` (fields + `get()`), `:116-122` (awake/destroy singleton).
- `core/components/network/netmgr/transport.py:14-52` (role properties), `:54-101` (host/connect/disconnect), `:103-145` (despawn), `:147-161` (`send_rpc(net_id, method, args, target)`).
- `core/components/network/netmgr/sync.py:14-56` (`spawn_entity`), `:58-198` (`spawn_prefab(path, pos, rot, owner)`), spawn-request/despawn handling.
- `core/network/rpc.py:15-30` (decorators), `:33-69` (`invoke_rpc` fallback chain), `:72-106` (directed send helpers).
- `core/components/network/network_identity.py:13-46` (fields + `AuthorityMode`), `:60-107` (authority/ownership).
- `core/components/network/network_transform.py:25-51` (`send_rate = 20.0`), `network_variables.py:36-39` (`send_rate = 10.0`), `:71-97` (var accessors), `:156-167` (rate-gated flush).
- `plugins/network_plugin.py:13-17` (`NetworkMessage`), `:94-115` (`send`/`broadcast`/`on_message`), `:118+` (`_dispatch_rpc`, `"*"` wildcard).
