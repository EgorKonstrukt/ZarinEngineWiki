# Networking Overview

Two independent subsystems share the transport layer:

- Gameplay netcode (components + NetworkPlugin): synchronize entities across a session — transforms, rigidbodies, animator state, custom variables, spawns and RPCs. Authoritative or owner-driven per component; the transport underneath is swappable.
- Editor collaboration (CollabServer / CollabClient): multiple editors in one session see cursors, camera positions, selections and gizmo operations live, with scene snapshots, asset watching and shared script editing.

Both ride on `core/network`: TCP transport with msgpack framing (`protocol.MessageType`, `transport`), a `GameServer`/`GameClient` pair, password rooms (`server.set_password/set_room/set_max_clients`), relay and UPnP helpers for NAT traversal, and an `rpc` module for remote calls.

Honest status: collaboration is a working real-time system; the gameplay side ships components, manager and plugin stubs (`host/connect/send/broadcast/poll`) ready to wire with any transport — WebRTC, ENet or raw sockets. Design gameplay code against the component APIs (ownership, snapshots, send rates); the wire format stays replaceable underneath.

Pages: Transport and Protocol, Network Plugin, NetworkManager, NetworkIdentity, NetworkTransform, NetworkRigidbody, NetworkAnimator, NetworkVariables, NetworkSpawn, NetworkPlayer, Collaboration, RemoteCollaborator.

First session recipe: empty scene → NetworkManager entity (port 7777, player prefab assigned, auto spawn on) → player prefab with NetworkIdentity + NetworkTransform + NetworkPlayer → host on one machine, connect from another → both spawns appear and move.
