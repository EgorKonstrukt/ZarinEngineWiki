# Networking Overview

Two independent subsystems share `core/network` (TCP + msgpack framing):

- Gameplay netcode (components + NetworkManager + NetworkPlugin) — implemented, real transport calls. `host()` / `connect()` / `send()` / `broadcast()` / `poll()` / `on_message()` in `plugins/network_plugin.py` delegate to the `core.network.transp` `get_transport()` singleton (`GameServer` / `GameClient` over TCP, `MessageType` framing). Syncs entities across a session: transforms, rigidbodies, animator state, variables, spawns, RPCs; authority per component (server / owner).
- Editor collaboration (`CollabServer` / `CollabClient` in `core/network/serverp/*` and `core/network/client.py`, `CollaborationManager` in `core/network/collab/*`): live cursors, cameras, selections, gizmo ops, scene snapshots, asset watch, co-edited scripts. Password rooms, relay and UPnP helpers belong to this path, not to gameplay.

Limits (gameplay path): TCP only, no UDP/WebRTC/ENet (`asyncio.start_server` / `asyncio.open_connection` with `TCP_NODELAY`); no password/room/auth on `GameServer` (JOIN handshake + `max_players` check only); `MAX_MESSAGE_SIZE` 4 MiB per frame; the server relays only `NET_TRANSFORM` / `NET_RIGIDBODY` / `NET_ANIMATOR` / `NET_VARIABLES` / `NET_RPC` / `NET_SNAPSHOT` to other peers; `poll()` must run every frame — one pump (plugin or manager), they share a single `Transport` deque.

Pages: Transport and Protocol, Network Plugin, NetworkManager, NetworkIdentity, NetworkTransform, NetworkRigidbody, NetworkAnimator, NetworkVariables, NetworkSpawn, NetworkPlayer, Collaboration, RemoteCollaborator.

First session recipe: empty scene → NetworkManager entity (port 7777, player prefab assigned, auto spawn on) → player prefab with NetworkIdentity + NetworkTransform + NetworkPlayer → host on one machine, connect from another → both spawns appear and move.

Related: Transport and Protocol (wire details), Network Plugin (RPC events), NetworkManager (session dispatch).

Verified against: `plugins/network_plugin.py:64-180` (`host`/`connect`/`send`/`broadcast`/`on_message`/`poll`); `core/network/transp/transport.py:82-144`; `core/network/transp/server.py:62-66,108-116,140-160,248`; `core/network/transp/client.py:58-64,91-129`; `core/network/protocol.py:80-110`; `core/config/constants.py:20-21`; `core/network/serverp/base.py:80-87,174-189` (password/room/auth are collab-only). No open markers in `core/network/transp/*`, `plugins/network_plugin.py`, `core/components/network/*` (grep).
