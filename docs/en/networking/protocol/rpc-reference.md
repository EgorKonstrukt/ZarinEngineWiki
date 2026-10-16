# RPC Reference

Remote calls travel as `NET_RPC = 103` in two different shapes. Do not mix them: plugin events use `{"rpc", "payload"}`, component calls use `{"net_id", "method", "args", ...}`. The server stamps `_sender` on relayed frames before dispatch.

## Shape A — plugin events (`NetworkPlugin`)

`send(msg_type, payload, target_id=-1)` wraps the call as `{"rpc": name, "payload": {...}}` and uses `send_to` when `target_id >= 0` on a server, otherwise `broadcast`; `broadcast(msg_type, payload)` always broadcasts (`plugins/network_plugin.py:94-113`).

```python
plug = engine.plugin_manager.get_plugin("NetworkPlugin")
plug.on_message("chat", lambda msg: print(msg.sender_id, msg.payload))
plug.broadcast("chat", {"text": "hello"})          # everyone
plug.send("ping1", {"t": 1.0}, target_id=peer_id)  # server -> one peer
plug.poll()  # every frame: dispatches plugin RPCs, re-queues the rest
```

Subscribe with `on_message(msg_type, handler)`; handlers receive a `NetworkMessage(msg_type, payload, sender_id)`. `_dispatch_rpc` looks up handlers by `rpc` name and falls back to `"*"` handlers (`network_plugin.py:115-142`). `handle_transport_messages` splits `NET_RPC {"rpc": ...}` frames off for the plugin and returns the remainder; `poll()` drains the transport and pushes leftovers back to the front of the queue (`network_plugin.py:144-180`).

## Shape B — component calls (`NetworkManager` + `core/network/rpc.py`)

`NetworkManager.send_rpc(net_id, method, args=None, target=None)` builds `{"net_id", "method", "args", "t", "target?"}` and routes: `send_to` when `target` is set on a server, `broadcast` when on a server or `target` is `None`, otherwise `send` to the server (`netmgr/transport.py:147-161`).

```python
mgr = NetworkManager.get()
mgr.send_rpc(net_id, "take_damage", {"amount": 10})            # server fans out / client asks server
mgr.send_rpc(net_id, "heal", {"amount": 5}, target=peer_id)    # server -> one peer

from core.ecs import Component
class Health(Component):
    @server_rpc
    def take_damage(self, args): ...
```

Helpers in `core/network/rpc.py:72-105`: `send_server_rpc` (client sends, server broadcasts), `send_client_rpc` (server broadcasts only), `send_target_rpc` (server `send_to`, client plain `send`). Decorators `server_rpc` / `client_rpc` / `target_rpc` tag a method with `_rpc_type` and register it by name. `invoke_rpc(entity, method, args, sender)` tries the tagged method first (`fn(args)`, falling back to `fn()`), then `handle_rpc(method, args, sender)`, then `rpc_<method>(**args)`.

## Dispatch order

`NetworkManager.on_update` checks `NET_RPC` frames first: frames containing `"rpc"` go to the plugin's `_dispatch_rpc`, everything else goes to `_handle_rpc` (`netmgr/manager.py:160-172`). `_handle_rpc` drops frames whose `target` is not the local id, skips `client`-type RPCs on the server, and invokes the rest on the entity found by `net_id` (`netmgr/sync.py:323-353`).

Related: Message Types (NET_RPC row), Snapshots and Interpolation (`apply_snapshot` dispatch), NetworkManager session component.

Verified against: `plugins/network_plugin.py:94-180`; `core/components/network/netmgr/transport.py:147-161`; `core/components/network/netmgr/manager.py:124-131,160-172`; `core/components/network/netmgr/sync.py:323-353`; `core/network/rpc.py:12-106`; `core/network/transp/server.py:232-263`.
