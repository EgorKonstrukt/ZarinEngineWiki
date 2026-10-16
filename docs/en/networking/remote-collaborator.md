# RemoteCollaborator

In-editor visual stand-in for a remote peer: where they look, what they hold selected, what they drag.

A 46-line component (`core/components/network/remote_collaborator.py`) with no custom `_inspector_fields` — presence is the data (identity lives in `peer_id` / `peer_name` / `color`, persisted via `serialize`/`deserialize`). It renders `gizmo_lines` / `gizmo_meshes` every frame: a square marker plus stem at the peer transform in the peer's color (`gizmo_meshes` returns `[]`).

Peers appear when collaboration connects and vanish on disconnect timeout; the CollaborationManager owns the mapping from peer id to entity. Colors stay stable per peer within a session so teams learn who is who.

Use while co-editing: follow the frustum to see what your partner stages, avoid grabbing their selected subtree, watch their gizmo drag before it lands to catch mistakes early.

Troubleshooting: peer missing → not connected or timed out; stale pose → op-stream stalled, check latency; wrong color → session rejoined, colors reassign.

Related: Collaboration (sessions), NetworkPlayer (gameplay twin of peer data), Gizmos and Icons (rendering passes).
