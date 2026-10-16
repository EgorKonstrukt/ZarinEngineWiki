# Networking

Two real subsystems on one TCP/msgpack transport (`core/network`): gameplay netcode and editor collaboration. Gameplay transport is implemented — `GameServer`/`GameClient` over TCP with `NET_*` message dispatch — not a stub (see fact note in the overview page).

- Networking Overview — the two subsystems, honest status, first-session recipe.
- Transport and Protocol — `Transport` singleton, `MessageType` framing, RPC module.
- Network Plugin — `NetworkPlugin` host/connect/send/broadcast/poll wiring.
- NetworkManager — session component, spawn tables, per-type dispatch.
- NetworkIdentity — net ids, ownership, authority modes.
- NetworkTransform — threshold send, interpolation, teleport.
- NetworkRigidbody — velocity sync, fixed-update path.
- NetworkAnimator — state/parameter sync, triggers.
- NetworkVariables — custom var replication.
- NetworkSpawn — prefab spawn API.
- NetworkPlayer — per-peer data (ready/score/team/name).
- Collaboration — `CollaborationManager` editor sessions.
- RemoteCollaborator — remote peer representation component.
