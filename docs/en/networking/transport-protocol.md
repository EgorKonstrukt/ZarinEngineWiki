# Transport and Protocol

Wire layer under gameplay netcode and collaboration: TCP sockets, 4-byte big-endian length prefix + msgpack frames, typed messages, per-frame drain.

## Gameplay transport (`core/network/transp/`, singleton via `get_transport()`)

| API | Signature / meaning |
|---|---|
| `host` | `host(host="0.0.0.0", port=7777, max_players=16) -> bool` — starts `GameServer` in a thread, resets role to server, `local_id = 0` |
| `connect` | `connect(host="127.0.0.1", port=7777, name="Player") -> bool` — starts `GameClient`, sends `JOIN {"name"}`, waits up to 5 s, returns `False` on `NET_KICK` |
| `disconnect` | `disconnect()` — stops client/server, clears the queue, role back to `"none"` |
| `send` | `send(msg_type, data)` — client → server; on server role acts as broadcast |
| `send_to` | `send_to(peer_id, msg_type, data)` — server → one peer; on client role falls back to `send` to server |
| `broadcast` | `broadcast(msg_type, data)` — alias of `send` (server fans out to all peers) |
| `poll` | `poll() -> list[(msg_type, data)]` — drain incoming without blocking; call every frame |
| `peek` / `get_peers` | `peek()` non-destructive read; `get_peers() -> [{id, name}]` on server |
| State | `is_connected`, `is_server`, `is_client`, `local_id` (server `0`, client = assigned peer id), `peer_count`, `bytes_sent`, `bytes_received` |
| Caps / callbacks | `set_max_players(n)` lives on `GameServer`, not on `Transport`; `set_on_connected` / `set_on_disconnected` live on `GameClient`, not on `Transport` |

## Protocol (`core/network/protocol.py`, `core/config/constants.py`)

- Framing: `make_msg(msg_type, data)` packs msgpack `{"t": int, "d": dict}` with a `">I"` length prefix; `FRAME_HEADER_SIZE = 4`; payloads over `MAX_MESSAGE_SIZE` (4 MiB) raise instead of sending; `parse_msg` is the inverse.
- `MessageType` IntEnum: gameplay range `NET_SPAWN = 100`, `NET_DESPAWN = 101`, `NET_SPAWN_REQUEST = 102`, `NET_RPC = 103`, `NET_TRANSFORM = 104`, `NET_RIGIDBODY = 105`, `NET_ANIMATOR = 106`, `NET_OWNER_CHANGE = 107`, `NET_VARIABLES = 108`, `NET_READY = 109`, `NET_KICK = 110`, `NET_SNAPSHOT = 111` (collaboration types occupy 1–55, 210–212).
- RPC shapes: plugin events travel as `NET_RPC {"rpc": name, "payload": {...}}` with the server stamping `_sender` before dispatch; component calls travel as `NET_RPC {"net_id", "method", "args", "rpc_type", "target?"}` via `rpc.send_server_rpc` / `send_client_rpc` / `send_target_rpc`.

## Collaboration transport (separate path, do not confuse)

`CollabServer` (`core/network/serverp/base.py`, `connection.py`): `set_password` / `set_room` / `set_max_clients`, `_auth_ok` (room + token check), `update_scene_data`. `CollabClient` (`core/network/client.py`): `connect(host, port, name, password, room)`, `set_on_auth_failed`. Relay (`core/network/relayp/`) and UPnP (`core/network/upnpp/`) helpers serve this path. Gameplay `GameServer` has none of that: first frame must be `JOIN`, over-capacity peers get `NET_KICK {"reason": "server_full"}` and the socket closes.

## Limits

TCP only — `asyncio.start_server` / `asyncio.open_connection` with `TCP_NODELAY`, so high-rate state pays head-of-line blocking; no UDP channel, no encryption, no gameplay-side auth. The server relays only transform/rigidbody/animator/variables/RPC/snapshot to *other* peers; spawn requests, ownership changes and despawns are consumed by `NetworkManager`. `Transport.send` on a server broadcasts — targeted delivery needs `send_to`; a client cannot unicast, it always sends to the server. `NetworkPlugin.poll()` and `NetworkManager.on_update()` drain the same singleton deque, so run one pump per frame (the manager forwards `NET_RPC {"rpc": ...}` frames to the plugin).

Usage: `host()` once on the host machine, `connect()` on joiners, then `poll()` every frame and dispatch by `MessageType`. Keep payloads flat and tiny (ids, positions, enums); large transfers go through `split_chunks` (`CHUNK`) or snapshots, never per-frame whole scenes.

Troubleshooting: connect refused → server not started or port blocked/firewalled; kicked at join → `server_full`; silent peers → `poll()` not called each frame or two pumps fighting over one queue; oversize send → `ValueError`, split the payload.

Related: Network Plugin (gameplay wiring), NetworkManager (session component), Collaboration (editor sessions), RPC usage in `NetworkManager.send_rpc`.

Verified against: `core/network/transp/transport.py:39-80,82-157`; `core/network/transp/server.py:59-66,108-124,140-160,223-263,269-317`; `core/network/transp/client.py:52-64,91-129,148-186`; `core/network/protocol.py:26-111,147-163`; `core/network/rpc.py:72-105`; `core/config/constants.py:20-21`; `core/network/serverp/base.py:80-87,174-189,233-234`; `core/components/network/netmgr/manager.py:124-172`.
