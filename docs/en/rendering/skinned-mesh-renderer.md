# Skinned Mesh Renderer

Draws skeletal-animated meshes with GPU skinning: characters, creatures, animated props.

| Inspector group / field | Meaning |
|---|---|
| Mesh: Mesh | Animated mesh source with bone weights |
| Mesh: Source | Bind source selector |
| Skinned Mesh Renderer: Materials | Material slots, one per submesh |
| Cast Shadows / Receive Shadows | Shadow toggles |
| Update When Offscreen | Keep skinning culled meshes (correct shadows and probes) |

Bone weights arrive with the mesh from Assimp (positions, normals, UVs, tangents, colors, weights in one import). The animation system (clips, Animator, controllers) writes bone poses into the Armature every frame; the skinned pass uploads bone matrices once per mesh and skins all vertices on the GPU, so CPU cost stays flat regardless of vertex count.

Workflow: import the rigged FBX, add Armature (bone names must match), add SkinnedMeshRenderer with materials, attach the Animator with its controller, verify in Play — then enable Update When Offscreen only for shadow-casting crowds.

Blend Shapes layer morph targets over the skeleton for faces (see Blend Shapes page).

Troubleshooting: rigid T-pose → armature mismatch or animator missing; exploding vertices → weight/normalization import issue, reimport with tangents; shadows frozen → Update When Offscreen off on a culled caster.

Related: Armature, Blend Shapes, Animation Clips and Animator, MeshRenderer (static), PhysBone (physical secondary motion).
