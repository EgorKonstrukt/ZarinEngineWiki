# Collaboration

Real-time multi-editor sessions over TCP with msgpack framing: several humans, one project, live cursors, cameras, selections and gizmo operations.

What syncs live: peer cursors and viewport cameras, entity selections, gizmo drag operations, scene snapshots on demand, asset changes via the file watcher, open tabs, script contents with per-peer cursors and operational-transform edits.

Entry points (`core/network/collaboration.py`, ~2300 lines):

- `CollabServer`: hosts the room — peer registry with join/leave callbacks (`set_peer_joined_callback`, `set_peer_left_callback`), scene snapshot serving (`set_scene_snapshot_callback`), broadcast of remote scene/tab/script events.
- `CollabClient`: joins — connection-change and auth-failure hooks (`set_on_connection_change`, `set_on_auth_failed`), senders for script open/change/cursor (`send_script_open/change/cursor`), receivers for remote scene open, tab switch/close, script ops.
- Background `_PollThread` drains the socket without blocking the editor; `_AssetWatcher` thread pushes file changes.
- CollaborationManager (editor side) owns state sync, peer discovery and disconnect timeouts; the panel exposes host/join, peer list and kick.

Session flow: host starts CollabServer from the collaboration panel → peers join with the address → each peer's camera and cursor appear as RemoteCollaborator gizmos → edits broadcast as operations → conflicts resolve by operation order, snapshots heal divergence.

Etiquette that prevents pain: one peer owns a scene subtree at a time (selection broadcast shows who); heavy reimports pause for a countdown; script conflicts prefer the operation stream over copy-paste overwrites.

Troubleshooting: peer invisible → firewall or wrong address; edits lost → divergence beyond op-stream, request a fresh snapshot via `set_on_scene_sync`; high latency → asset watcher flooding on huge imports, exclude build/cache dirs.

Related: RemoteCollaborator (peer gizmos), Transport and Protocol (wire layer), VCS panel (commit the collaborated result).
