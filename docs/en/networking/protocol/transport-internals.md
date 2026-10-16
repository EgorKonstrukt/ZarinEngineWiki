# Transport Internals

How `GameServer` / `GameClient` move bytes: one TCP connection per peer, each in its own `asyncio` loop on a daemon thread, frames drained per write. Singleton access via `get_transport()`.

## `GameServer` (`core/network/transp/server.py`)

`start()` spawns the loop thread and waits up to 5 s for the ready flag; `set_max_players(n)` clamps to `>= 1` at any time. The listener is `asyncio.start_server` with `TCP_NODELAY` set on both the listen sockets and every accepted socket (`server.py:108-116,126-133`). `_read_frame` reads exactly `FRAME_HEADER_SIZE` bytes, unpacks `">I"`, rejects `0` or `> MAX_MESSAGE_SIZE`, then reads the payload (`server.py:118-124`).

Join handshake, first frame decides everything:

1. First frame must be `JOIN`, otherwise the socket is closed without a reply (`server.py:140-147`).
2. At capacity (`len(clients) >= max_players`) the server writes `NET_KICK {"reason": "server_full"}`, drains, and closes (`server.py:148-160`).
3. Otherwise it assigns `peer_id` from `_next_id` (starts at 1), truncates the name to 32 chars, queues `PEER_JOINED {"id", "name"}` locally, fans it out to existing peers, and answers the newcomer with `JOINED {"your_id", "peers"}` (`server.py:161-188`).
4. On disconnect it queues `LEAVE {"id"}` locally and fans it out, then closes (`server.py:194-221`).

`_client_loop` answers `PING` with `PONG {"t"}` inline; every other accepted type gets `_sender` stamped, is appended to the shared incoming deque, and — only for transform/rigidbody/animator/variables/RPC/snapshot — is re-packed and written to all *other* peers (`server.py:223-267`). `broadcast` writes one packed message to every client; `send_to(peer_id, ...)` writes to a single client; both schedule the drain on the loop via `run_coroutine_threadsafe` (`server.py:269-317`).

## `GameClient` (`core/network/transp/client.py`)

`connect(host, port, name)` spawns the loop thread (no-op if already connected). `_connect` uses `asyncio.open_connection`, sets `TCP_NODELAY`, marks connected, then sends `JOIN {"name"}` as its **first** frame and waits for the reply (`client.py:91-104`). `JOINED` stores `peer_id` and queues the frame; `NET_KICK` is queued and the session ends; anything else unparseable aborts the connect. `_read_loop` answers `PING` with `PONG`, queues `NET_KICK` and breaks, and queues everything else (`client.py:148-177`). `send(msg_type, data)` packs and schedules `_async_send` only while connected with a live loop; `bytes_sent` / `bytes_received` count framed bytes. `set_on_connected` / `set_on_disconnected` are the only callbacks — they live on `GameClient`, not on `Transport`.

## `Transport` facade (`core/network/transp/transport.py:82-144`)

| Method | Semantics |
|---|---|
| `host(host="0.0.0.0", port=7777, max_players=16)` | Disconnects first, starts `GameServer`; on success role = server, `local_id = 0` |
| `connect(host="127.0.0.1", port=7777, name="Player")` | Disconnects first, starts `GameClient`; polls up to **5 s** for `connected`, returns `False` immediately on `NET_KICK` |
| `disconnect()` | Stops client and server, role back to `"none"`, clears the incoming queue |
| `send(msg_type, data)` | Client → server; on a server acts as **broadcast** |
| `send_to(peer_id, msg_type, data)` | Server → one peer; on a client falls back to plain `send` to the server |
| `broadcast(msg_type, data)` | Alias of `send` |
| `poll()` | Drain the shared deque under lock, non-blocking — call once per frame |
| `peek()` / `get_peers()` | Non-destructive read; `get_peers() -> [{id, name}]` on server only |

State: `is_server` / `is_client` / `is_connected`, `local_id` (server `0`, client = assigned id), `peer_count`, `bytes_sent` / `bytes_received` (client side only). TCP only — no UDP, no encryption, no gameplay-side auth; high-rate state pays head-of-line blocking. `NetworkPlugin.poll()` and `NetworkManager.on_update()` drain the same deque, so run exactly one pump per frame.

Related: Message Types (relay table), Transport and Protocol (overview), NetworkManager.

Verified against: `core/network/transp/transport.py:39-80,82-157`; `core/network/transp/server.py:29-66,97-124,126-221,223-317`; `core/network/transp/client.py:19-64,77-129,148-194`; `core/network/protocol.py:97-118`; `core/config/constants.py:20-21`.
