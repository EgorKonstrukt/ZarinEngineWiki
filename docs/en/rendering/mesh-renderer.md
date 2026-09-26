# MeshRenderer

Answers *how* to draw the `MeshFilter` mesh: material slots, shadow participation, UV transforms.

| Field | Default | Meaning |
|---|---|---|
| materials | — | Material slots (`{path}` each, e.g. `.zpem`), one per submesh in order |
| cast_shadows | on | Write into shadow maps |
| receive_shadows | on | Sample shadow maps in the main pass |
| uv_scale / uv_offset | (1,1) / (0,0) | Tiling and offset applied by the shader UV override |
| uv_scale_by_transform | off | Derive tiling from the Transform scale (floors, walls) |
| sprite_texture | — | Optional billboard texture override |
| dynamic_reflections | off | Refresh environment reflections per frame instead of on demand |

Draw order and cost: one draw call per visible submesh after frustum culling; transparent materials sort into the transparent bucket by depth. Casters render into every covering shadow map, so a high-poly hero with cast_shadows on multiplies shadow cost — disable casting for small props that never readably shadow.

UV workflow: tiling brick/concrete via uv_scale (4,4) instead of duplicating geometry; scrolling effects by animating uv_offset from a script (conveyors, water slides); world-aligned trim via uv_scale_by_transform on architecture.

For animated meshes use SkinnedMeshRenderer; for in-editor blockout use the ProBuilder-style `probuilder_mesh` component; for collision use MeshCollider on the same filter path.

Troubleshooting: invisible mesh → filter path broken or all materials missing; wrong submesh material → slot order mismatch, reorder the list; shadows absent → cast on but light beyond distance, or receive off on the ground; shimmering UVs → non-uniform negative scale, fix the Transform.

Related: MeshFilter, Materials, Shaders (UV override uniforms), Shadows, SkinnedMeshRenderer.
