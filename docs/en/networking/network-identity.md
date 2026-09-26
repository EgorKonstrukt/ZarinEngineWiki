# NetworkIdentity

Network existence card for an entity: who it is on the wire, who owns it, which prefab to instantiate remotely, whether it survives scene changes.

| Inspector group / field | Meaning |
|---|---|
| Network Identity: Net ID | Session-unique id, assigned on spawn |
| Owner ID | Peer id with write authority |
| Prefab ID | Which prefab remotes instantiate |
| Authority | AuthorityMode — owner-driven or server-authoritative |
| Is Local Player | This peer's avatar flag |
| Dont Destroy | Survive scene loads (managers, session state) |

API: `transfer_ownership(peer_id)`, `request_ownership()`, `apply_owner_change(...)` — handoff protocol for pickups, vehicles and dropped items.

Authority model: owner-driven components (transform, variables) accept writes from the Owner ID peer; server-authoritative flows route through the host. `AuthorityMode` on the component selects the posture per entity — players owner-driven, doors and scores server-side.

Every networked prefab needs exactly one NetworkIdentity at its root; sync components (transform, rigidbody, animator, variables) attach alongside and key off the Net ID. `Is Local Player` gates input scripts — same prefab, only the owner reads keys.

Ownership handoff pattern: driver enters car → `request_ownership()` → host approves → `transfer_ownership(driver)` → car transform follows the driver until exit hands it back.

Troubleshooting: duplicates on remotes → prefab id mismatch or missing identity on root; input on all clones → `Is Local Player` gate missing; handoff ignored → request sent to non-authority, route via host.

Related: NetworkManager (spawns assign ids), NetworkTransform/Rigidbody/Animator/Variables (sync off this id), NetworkPlayer (peer data twin).
