# Mesh Collider

Collision from mesh geometry: static scenery exact, dynamic bodies convex-approximated.

| Field | Default | Meaning |
|---|---|---|
| center | origin | Local offset |
| mesh_path | — | Source mesh file |
| collision_mode | AUTO | AUTO / MESH / CONVEX_HULL / BOX / SPHERE |
| max_vertices | 2000 | Simplification budget |

Effective mode: AUTO becomes MESH for static bodies and CONVEX_HULL for dynamic ones; an explicit MESH on a dynamic body is lowered to CONVEX_HULL. A body counts as dynamic with an active positive-mass Rigidbody (kinematic included) that is not a trigger.

Prefer primitives and compounds for anything that moves; reserve mesh collision for static level geometry.

Related: MeshFilter, Rigidbody, Compound notes in Physics Overview.
