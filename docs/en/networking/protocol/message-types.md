# Gameplay Message Types (NET_*)

Wire IDs for gameplay netcode: `MessageType` IntEnum in `core/network/protocol.py:80-91`. Values 100–111; collaboration types occupy 1–55 and 210–212.

## Frame format

`make_msg(msg_type, data)` packs msgpack `{"t": int(msg_type), "d": dict}` and prepends a `">I"` (big-endian uint32) length prefix; `parse_msg` is the inverse, `pack_frame` / `unpack_frame` are aliases. `FRAME_HEADER_SIZE = 4`, `MAX_MESSAGE_SIZE = 4 MiB` — oversize payloads raise `ValueError` instead of sending. Large transfers go through `split_chunks` (`CHUNK`) instead of one frame.

## Server intake vs relay

Every client frame first passes `GameServer._client_loop`: only the allowlisted types are queued locally with a `_sender` stamp (`server.py:232-247`). Of those, only transform/rigidbody/animator/variables/RPC/snapshot are fanned out to *other* peers (`server.py:248-263`). Spawn requests, ownership changes, despawns and ready flags are consumed by `NetworkManager`, never relayed. Related handshake types: first frame must be `JOIN`, answered with `JOINED`; disconnects fan out as `LEAVE`; `PING` is answered with `PONG` inline and never queued.

## Type table

| Type | Value | Purpose (one line) | Who sends / relay |
|---|---|---|---|
| `NET_SPAWN` | 100 | Server announces a new entity `{net_id, owner_id, prefab_id, entity}` | Server `broadcast` (or `send_to` for a late joiner via `_sync_new_peer`); clients apply via `_handle_spawn` |
| `NET_DESPAWN` | 101 | Remove entity `{net_id}` | Server broadcasts, client sends as a request to server; accepted by server but **not** relayed |
| `NET_SPAWN_REQUEST` | 102 | Client asks server to spawn `{prefab_id, entity, requester, pos?, rot?}` | Client → server only; server-only `_handle_spawn_request`, never relayed |
| `NET_RPC` | 103 | Remote call, two shapes: plugin `{"rpc", "payload"}` or component `{"net_id", "method", "args", "rpc_type", "target?"}` | Both directions; **relayed** to other peers; see RPC Reference |
| `NET_TRANSFORM` | 104 | Transform state `{net_id, t, seq, p?, r?, s?}` | Authority side broadcasts; **relayed**; applied via `NetworkTransform.apply_snapshot` |
| `NET_RIGIDBODY` | 105 | Physics state `{net_id, t, seq, v?, w?}` | Authority side broadcasts (from `on_fixed_update`); **relayed**; applied via `NetworkRigidbody.apply_snapshot` |
| `NET_ANIMATOR` | 106 | Animator state `{net_id, t, seq, clip?, time?, speed?, playing?, params?, trigger?}` | Authority side broadcasts; **relayed**; applied via `NetworkAnimator.apply_snapshot` |
| `NET_OWNER_CHANGE` | 107 | Ownership transfer; client sends `{requester}`, server rebroadcasts `{net_id, owner_id}` | Client → server → `broadcast`; accepted by server but **not** auto-relayed |
| `NET_VARIABLES` | 108 | Synced vars `{net_id, vars, t, seq}` (legacy `data` key also read) | Authority side broadcasts; **relayed**; applied via `NetworkVariables.apply_snapshot` or generic `setattr` fallback |
| `NET_READY` | 109 | Reserved ready flag | Accepted and queued by the server, **not** relayed, no built-in `NetworkManager` branch |
| `NET_KICK` | 110 | Force-disconnect `{reason}`, e.g. `server_full` on over-capacity join | Server → one peer, then closes the socket; `Transport.connect` returns `False` when it sees one |
| `NET_SNAPSHOT` | 111 | Reserved whole-world snapshot channel | Accepted and **relayed** like state channels; no built-in emitter or consumer — user payloads |

Related: Transport Internals (framing on the wire), RPC Reference (NET_RPC shapes), Snapshots and Interpolation (state channels).

Verified against: `core/network/protocol.py:26-94,97-118`; `core/config/constants.py:20-21`; `core/network/transp/server.py:118-124,140-160,223-263`; `core/network/transp/client.py:91-129,148-177`; `core/network/transp/transport.py:92-110`; `core/components/network/netmgr/manager.py:132-222`; `core/components/network/netmgr/sync.py:200-379`.
