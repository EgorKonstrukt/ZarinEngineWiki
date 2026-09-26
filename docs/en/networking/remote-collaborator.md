# RemoteCollaborator

In-editor visual stand-in for a remote peer: where they look, what they hold selected, what they drag.

A 54-line component with no Inspector fields — presence is the data. It renders `gizmo_lines` / `gizmo_meshes` every frame: peer camera frustum, cursor ray, selection highlight and active gizmo operation handles in the peer's color.

Peers appear when collaboration connects and vanish on disconnect timeout; the CollaborationManager owns the mapping from peer id to entity. Colors stay stable per peer within a session so teams learn who is who.

Use while co-editing: follow the frustum to see what your partner stages, avoid grabbing their selected subtree, watch their gizmo drag before it lands to catch mistakes early.

Troubleshooting: peer missing → not connected or timed out; stale pose → op-stream stalled, check latency; wrong color → session rejoined, colors reassign.

Related: Collaboration (sessions), NetworkPlayer (gameplay twin of peer data), Gizmos and Icons (rendering passes).
