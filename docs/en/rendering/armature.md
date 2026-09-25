# Armature

Bone hierarchy container driving skinned meshes and PhysBone chains.

| Inspector group / field | Meaning |
|---|---|
| Bone: Bone Name | Human-readable joint name |
| Bone: Bone Index | Solver-side index |
| Armature: Bone Count | Total joints |
| Armature: Root Bone | Hierarchy root |

The Animator writes poses into the armature; SkinnedMeshRenderer and PhysBone read them back. Keep bone names stable — retargeting and prefab overrides match by name.

Related: Skinned Mesh Renderer, PhysBone, Animation Clips and Animator.
