# Skinned Mesh Renderer

Draws skeletal-animated meshes with GPU skinning.

| Inspector group / field | Meaning |
|---|---|
| Mesh: Mesh | Animated mesh source |
| Mesh: Source | Bind source selector |
| Skinned Mesh Renderer: Materials | Material slots, one per submesh |
| Cast Shadows / Receive Shadows | Shadow toggles |
| Update When Offscreen | Keep skinning visible-culled meshes (for shadows and probes) |

Bone weights arrive with the mesh from Assimp; the animation system (clips, Animator, controllers) writes bone poses every frame, and the skinned pass uploads bone matrices once per mesh.

Related: Armature, Blend Shapes, Animation Clips and Animator, MeshRenderer (static meshes).
