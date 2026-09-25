# MeshRenderer

Answers *how* to draw the `MeshFilter` mesh.

| Field | Default | Meaning |
|---|---|---|
| materials | — | Material slots (`{path}` each, e.g. `.zpem`), one per submesh |
| cast_shadows | on | Write into shadow maps |
| receive_shadows | on | Sample shadow maps |
| uv_scale / uv_offset | (1,1) / (0,0) | Tiling and offset |
| uv_scale_by_transform | off | Derive tiling from the Transform |
| sprite_texture | — | Optional billboard texture override |
| dynamic_reflections | off | Refresh environment reflections per frame |

Geometry uploads once through the geometry cache; per-frame cost is one draw per visible submesh. For animated meshes use SkinnedMeshRenderer; for in-editor modeling use the ProBuilder-style mesh component.

Related: MeshFilter, Materials, Shaders, Shadows.
