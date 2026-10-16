# Collaboration Protocol

Editor co-editing sessions: a host plus guests share cursors, cameras, selections, gizmos, scene edits, scripts and assets. Separate path from gameplay netcode — string peer ids (`peer_<12hex>`), rooms, password auth. Implemented by `CollaborationManager` (`core/network/collab/manager.py` + `presence.py` / `poll.py` / `watcher.py` / `scene_sync.py` / `script_sync.py` / `asset_sync.py` / `upnp.py`).

## Server, client, auth

`CollabServer(host="0.0.0.0", port=9876, password="", room="", max_clients=32, heartbeat_timeout=20.0)` (`serverp/base.py:47-62`, default direct port `DEFAULT_DIRECT_PORT = 9876`). First frame must be `JOIN`; over capacity answers `NET_KICK {"reason": "server_full"}`; then `_auth_ok` checks the room (must match when the server sets one) and the password — `token = sha256(room + "\x00" + password)` or the plain password — replying `AUTH_FAIL {"reason": "auth"}` on mismatch; clients newer than `PROTOCOL_VERSION + 2` get `AUTH_FAIL {"reason": "version"}` (`serverp/base.py:174-189`, `serverp/connection.py:36-96`). Each peer gets a rotating color; joins fan out as `PEER_JOINED`, the newcomer receives `JOINED {your_id, your_color, peers, scene, version, room}`; leaves fan out as `LEAVE`. Guards: 400 msgs/s per-peer rate limit, 5 s stale sweeper vs `heartbeat_timeout`, `PING`→`PONG`, `TCP_NODELAY` everywhere.

`CollabClient.connect(host, port, name, password="", room="", timeout=8.0)` sends `_make_join` = `{name, version, room, token}` with `TCP_NODELAY` + `SO_KEEPALIVE`; `wait_connected(timeout)` resolves id vs auth error (`client.py:77-104,182-189`). `set_password` / `set_room` / `set_max_clients` reconfigure the server live; `update_scene_data(scene)` stores the snapshot served to newcomers (`serverp/base.py:80-87,233-234`).

## Presence, scenes, scripts, assets

`PresenceMixin` (`collab/presence.py`): `send_cursor(x, y, hit?)`, `send_camera(pos, fwd, up)`, `send_gizmo_state(mode, hover_axis, dragging)`; oversize payloads auto-split into `CHUNK` frames (`_send_link` / `_send_chunked`). Remote state lands on `RemotePeer` fields: `cursor_screen` / `cursor_hit`, `camera_pos`/`_fwd`/`_up`, `selected_entity_ids`, `gizmo_mode`/`_hover_axis`/`_dragging`, `current_tab` (`collab/models.py:9-34`). Default cadences in `CollabSettings`: cursor 1/30 s, camera 1/15 s, transform 1/20 s, gizmo 1/10 s, ping 3 s, poll 8 ms, scene sync 2 s (`collab/models.py:35-52`).

Scene edits (`scene_sync.py` + server `_route_message`): `send_entity_create/delete/transform/selection/component_update/component_sync/component_add/component_remove/play_mode`, each rebroadcast under its `*_UPDATE`/`*_CREATED`/… pair with the sender `id` stamped, plus `SCENE_OPEN` / `SCENE_TAB_SWITCH` / `SCENE_TAB_CLOSE` tab tracking, `request_scene_snapshot()` → server answers `SCENE_SNAPSHOT` to the requester only, and `_send_full_scene_sync(target)` replays entities + transforms to a new peer. Script co-editing (`script_sync.py`): `send_script_open/change/cursor(pos, sel_anchor, sel_end)/ops` with `set_on_remote_script_*` callbacks. Assets (`asset_sync.py` + `watcher.py`): `request_asset_list` / `send_asset_list(target)` (paths + sha256 checksums) / `send_asset_file` / `send_asset_request`; `_AssetWatcher` polls mtimes (interval ≥ 0.5 s) and fires change/delete callbacks.

Polling (`poll.py`): `_PollThread(callback, interval_ms, stop_event)` daemon loops drive `_poll_messages`, `_send_ping` and `_sweep_peers` from the manager.

## Example session

```python
mgr.start_server(host="0.0.0.0", port=9876, password="s3cret", room="LEVEL1")
invite = mgr.create_invite(public=True)          # "ZARIN1:..." (external IP when UPnP mapped)
mgr.join_invite(invite, name="Guest", password="s3cret")  # guest side: direct or relay by mode
mgr.send_cursor(x, y, hit_pos)                   # presence, ~30 Hz
mgr.send_script_change("scripts/player.py", text)
mgr.request_scene_snapshot()                     # full scene pull
mgr.stop()                                       # clears peers, watchers, UPnP mappings
```

Related: Relay and UPnP (rooms/invites/NAT), Message Types (frame format), RemoteCollaborator (in-scene avatars).

Verified against: `core/network/serverp/base.py:47-87,89-189,204-234`; `core/network/serverp/connection.py:18-129,152-263`; `core/network/client.py:77-104,182-200`; `core/network/collab/manager.py:88-157,194-320`; `core/network/collab/presence.py:50-125,140-267`; `core/network/collab/scene_sync.py:18-100`; `core/network/collab/script_sync.py:9-59`; `core/network/collab/asset_sync.py:49-80`; `core/network/collab/watcher.py:10-73`; `core/network/collab/poll.py:8-20`; `core/network/collab/models.py:9-52`.
