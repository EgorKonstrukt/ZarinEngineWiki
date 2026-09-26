# Mesh Collider

Collision from mesh geometry for cases primitives cannot fit: statues, terrain chunks, vehicle hulls, concave scenery.

| Field | Default | Meaning |
|---|---|---|
| center | origin | Local offset |
| mesh_path | — | Source mesh file (same import as MeshFilter) |
| collision_mode | AUTO | AUTO / MESH / CONVEX_HULL / BOX / SPHERE fallback approximations |
| max_vertices | 2000 | Simplification budget for generated hulls |

Effective mode logic, verified: AUTO becomes MESH for static bodies (exact triangles) and CONVEX_HULL for dynamic ones (stable approximation); an explicit MESH on a dynamic body is lowered to CONVEX_HULL automatically. A body counts as dynamic with an active positive-mass Rigidbody (kinematic included) that is not a trigger.

Cost reality: triangle-mesh vs dynamic body is the most expensive pairing in physics — it exists for static level geometry. Anything that moves gets primitives, compounds or convex hulls. `max_vertices` caps hull complexity; lower it until the gizmo still hugs the silhouette.

BOX/SPHERE modes are one-click downgrades when a mesh turns out to be roughly box- or ball-shaped — cheaper than re-authoring a collider.

Workflow: static statue — mesh collider AUTO (→MESH), no Rigidbody; moving wreck — AUTO (→CONVEX_HULL) with Rigidbody, or better a 3-box compound; terrain — prefer TerrainCollider over mesh.

Troubleshooting: falls through → dynamic body with MESH forced (check effective mode), hull too coarse, or tunneling; slow cooking on load → max_vertices huge, decimate the source; mismatch with visuals → mesh_path differs from the MeshFilter path.

Related: MeshFilter, Terrain Collider, Rigidbody, Compound notes in Physics Overview.
