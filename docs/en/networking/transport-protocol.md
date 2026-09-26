# Transport and Protocol

The wire layer under both gameplay netcode and collaboration: TCP sockets, msgpack frames, typed messages, connection callbacks.

Transport (`core/network/transport.py`, singleton via `get_transport()`):

- `host(bind, port, max_players)` / `connect(host, port, name)` / `disconnect()` — session lifecycle. Default game port 7777.
- `send(payload)` — to server (client side); `send_to(peer_id, type, data)` — server to one peer; `broadcast(type, data)` — server to all.
- `poll()` — drain incoming `[(msg_type, data)]` without blocking; call every frame.
- State: `is_connected`, `is_server`, `local_id`, `peer_count`; callbacks `set_on_connected`, `set_on_disconnected`.
- `set_max_players(n)` caps the room.

Protocol (`core/network/protocol.py`): `MessageType` IntEnum — every frame carries a type plus a dict payload. Gameplay RPCs travel as `NET_RPC` with `{"rpc": name, "payload": {...}}`; the server stamps the sender id before dispatch.

Server (`server.py`): `set_password`, `set_room`, `set_max_clients`, `start`, `stop`, `update_scene_data` (authoritative snapshot source). Client (`client.py`): connect/disconnect/send with `set_on_auth_failed` for wrong passwords. Relay (`relay.py`): forwarded sessions with auth-failure hooks. UPnP (`upnp.py`): automatic port-forward requests so hosts behind home routers accept direct connections.

RPC module (`rpc.py`): naming and dispatch conventions for remote calls shared by gameplay code and the editor bridge.

Framing note: msgpack keeps packets small and schema-free — dicts in, dicts out. Keep payloads flat and tiny (ids, positions, enums); never ship whole scenes per frame, ship snapshots and deltas.

Troubleshooting: connect refused → server not started or port blocked/firewalled; auth fails → password/room mismatch, handled by `set_on_auth_failed`; behind NAT → enable UPnP or relay mode; silent peers → `poll()` not called each frame.

Related: Network Plugin (gameplay wiring), NetworkManager (session component), Collaboration (editor sessions), RPC usage in NetworkManager.send_rpc.
