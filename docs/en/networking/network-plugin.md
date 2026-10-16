# Network Plugin

System plugin (`SYSTEM = True`) wiring gameplay code to the real transport: host, connect, message handlers and per-frame polling over `core.network.transp` `get_transport()` (`GameServer` / `GameClient` over TCP, msgpack `MessageType` framing).

```python
plug = engine.plugins.get("NetworkPlugin")
plug.host(port=7777, max_players=16)
plug.connect("192.168.1.10", 7777)
plug.on_message("chat", handle_chat)
plug.broadcast("chat", {"text": "hello"})
plug.poll()
```

API, verified:

| API | Signature / meaning |
|---|---|
| `host` | `host(port=7777, max_players=16) -> bool` — `get_transport().host("0.0.0.0", port, max_players)`, logs the result |
| `connect` | `connect(host, port=7777) -> bool` — `get_transport().connect(host, port, "Player")`, logs the result |
| `disconnect` | `disconnect()` — `get_transport().disconnect()`; also runs on plugin shutdown |
| `send` | `send(msg_type, payload, target_id=-1)` — `send_to(target_id, NET_RPC, ...)` when `target_id >= 0` and server, else `broadcast`; payload wrapped as `{"rpc": name, "payload": dict}` |
| `broadcast` | `broadcast(msg_type, payload)` — `broadcast(NET_RPC, {"rpc": name, "payload": dict})` to all peers |
| `on_message` | `on_message(msg_type, handler)` — subscribe; handler receives `NetworkMessage(msg_type, payload, sender_id)`; `"*"` gets everything unhandled |
| `poll` | `poll()` — `get_transport().poll()`, dispatch `NET_RPC {"rpc": ...}` frames to handlers (handler exceptions logged, never raised), requeue the rest to the front |
| State | `is_connected`, `is_server`, `local_id`, `peer_count` — direct reads of `get_transport()` |

Usage: one handler per message name, validate payload before use, `"*"` handler as the unknown-message logger. Call `poll()` once per frame from game code or a manager component — undrained queues read as dead peers. `NetworkManager.on_update()` already polls the same `Transport` deque and forwards `NET_RPC {"rpc": ...}` frames to this plugin, so do not run both pumps blindly.

```python
def handle_chat(msg):
    line = str(msg.payload.get("text", ""))
    Logger.info("chat from " + str(msg.sender_id) + ": " + line)

plug.on_message("chat", handle_chat)
plug.on_message("*", log_unknown)
```

Limits: TCP-only transport (no UDP/WebRTC/ENet); client `send` always goes to the server, targeted delivery is server-side `send_to`; no gameplay password/room/auth; 4 MiB per-frame cap (`MAX_MESSAGE_SIZE`).

Related: Transport and Protocol, NetworkManager (component-level sessions), NetworkVariables (state sync twin of RPC events).

Verified against: `plugins/network_plugin.py:13-17,33-62,64-84,94-116,118-180,182-188`; `core/network/transp/transport.py:82-144`; `core/network/protocol.py:80-91,97-110`.
