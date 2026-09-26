# Network Plugin

System plugin (`SYSTEM = True`) wiring gameplay code to the transport: host, connect, message handlers and per-frame polling.

```python
plug = engine.plugins.get("NetworkPlugin")
plug.host(port=7777, max_players=16)
plug.connect("192.168.1.10", 7777)
plug.on_message("chat", handle_chat)
plug.broadcast("chat", {"text": "hello"})
plug.poll()
```

API, verified:

- `host(port=7777, max_players=16)` — start a server on all interfaces; logs the result, returns bool.
- `connect(host, port=7777)` — join as `"Player"`; returns bool.
- `disconnect()` — leave; also runs on plugin shutdown.
- `send(msg_type, payload, target_id=-1)` — server-side targeted `send_to` when id ≥ 0, otherwise broadcast; everything travels as `NET_RPC`.
- `broadcast(msg_type, payload)` — to all peers.
- `on_message(msg_type, handler)` — subscribe; handler receives `NetworkMessage(msg_type, payload, sender_id)`. The `"*"` wildcard receives everything unhandled.
- `poll()` — drain transport, dispatch `NET_RPC` frames to handlers (handler exceptions logged, never raised), requeue the rest.
- State: `is_connected`, `is_server`, `local_id`, `peer_count`.

Handler pattern: one handler per message name, payload validated before use, wildcard handler as the unknown-message logger. Call `poll()` once per frame from game code or a manager component — undrained queues read as dead peers.

```python
def handle_chat(msg):
    line = str(msg.payload.get("text", ""))
    Logger.info("chat from " + str(msg.sender_id) + ": " + line)

plug.on_message("chat", handle_chat)
plug.on_message("*", log_unknown)
```

Related: Transport and Protocol, NetworkManager (component-level sessions), NetworkVariables (state sync twin of RPC events).
