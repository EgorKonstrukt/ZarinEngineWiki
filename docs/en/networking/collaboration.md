# Collaboration

Real-time multi-editor sessions over TCP with msgpack framing: several humans, one project, live cursors, cameras, selections and gizmo operations.

What syncs live: peer cursors and viewport cameras, entity selections, gizmo drag operations, scene snapshots on demand, asset changes via the file watcher, open tabs, script contents with per-peer cursors and operational-transform edits.

Entry points (`core/network/collab/*`, `core/network/client.py`, `core/network/serverp/*` — no single `collaboration.py`):

- `CollabServer` (`serverp/base.py` + `connection.py`): hosts the room — `set_password` / `set_room` / `set_max_clients`, `_auth_ok` room+token check, peer registry with join/leave callbacks (`set_peer_joined_callback`, `set_peer_left_callback` in `collab/presence.py`), scene snapshot serving (`set_scene_snapshot_callback`), broadcast of remote scene/tab/script events.
- `CollabClient` (`core/network/client.py`): joins — `connect(host, port, name, password, room)`, `set_on_auth_failed` / `set_on_connected` / `set_on_disconnected`, `send()` + `poll_messages()`; script domain senders for open/change/cursor (`send_script_open/change/cursor` in `collab/script_sync.py`), receivers for remote scene open, tab switch/close, script ops.
- Background `_PollThread` (`collab/poll.py`) drains the socket without blocking the editor; `_AssetWatcher` (`collab/watcher.py`) pushes file changes.
- CollaborationManager (`collab/manager.py`, 449 lines, editor side) owns state sync (`start_server(host, port, password, room)`, `connect(...)`, `host_relay` / `join_relay`, `create_invite` / `create_lan_invite` / `join_invite`, `stop`), peer discovery and disconnect timeouts; the panel exposes host/join, peer list and kick.

Session flow: host starts CollabServer from the collaboration panel → peers join with the address → each peer's camera and cursor appear as RemoteCollaborator gizmos → edits broadcast as operations → conflicts resolve by operation order, snapshots heal divergence.

Etiquette that prevents pain: one peer owns a scene subtree at a time (selection broadcast shows who); heavy reimports pause for a countdown; script conflicts prefer the operation stream over copy-paste overwrites.

Troubleshooting: peer invisible → firewall or wrong address; edits lost → divergence beyond op-stream, request a fresh snapshot via `set_on_scene_sync`; high latency → asset watcher flooding on huge imports, exclude build/cache dirs.

Related: RemoteCollaborator (peer gizmos), Transport and Protocol (wire layer), VCS panel (commit the collaborated result).
