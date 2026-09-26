# Network Plugin (Plugin Page)

System plugin (`network_plugin.py`, `SYSTEM = True`, `DESCRIPTION` "Multiplayer transport using GameServer/GameClient over TCP") bridging gameplay code and the transport singleton.

Plugin-side facts:

- Loads in the system tier during editor/engine startup; `initialize` logs readiness, `shutdown` disconnects cleanly so no session survives quit.
- Exposes session state as properties: `is_connected`, `is_server`, `local_id`, `peer_count` — all exception-guarded, safe to poll from UI code.
- `host(port=7777, max_players=16)` binds all interfaces; `connect(host, port)` joins as `"Player"`; both log and return bool.
- Messaging rides `MessageType.NET_RPC` frames: `send(type, payload, target_id)` targets one peer from the server or broadcasts otherwise; `broadcast(type, payload)` always fans out.
- `on_message(type, handler)` subscriptions feed `NetworkMessage(msg_type, payload, sender_id)`; `"*"` wildcard catches unhandled traffic for logging. `_dispatch_rpc` isolates handler exceptions per handler.
- `poll()` drains transport frames, dispatches RPC ones, and requeues the remainder back to the transport — non-RPC protocols coexist untouched.
- `handle_transport_messages(msgs)` is the seam other systems hook: returns unhandled frames for upstream processing.

For the full messaging patterns, payload shapes and session recipes see the Networking section (Transport and Protocol, NetworkManager, Network Plugin usage page) — this page covers the plugin as an engine citizen: where it loads, what it owns, how it shuts down.

Troubleshooting (plugin scope): handlers never fire → `poll()` not pumped or subscribed after traffic started; double handling → same handler subscribed twice (subscriptions append); session survives editor quit → shutdown path bypassed, always disconnect explicitly in tooling.

Related: Networking section (protocol, manager, components), Plugin Manager (system tier), PluginBase Lifecycle (hooks used).
