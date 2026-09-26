# Armature

Bone hierarchy container driving skinned meshes and PhysBone chains — the skeleton asset in the scene.

| Inspector group / field | Meaning |
|---|---|
| Bone: Bone Name | Human-readable joint name (retargeting matches by name) |
| Bone: Bone Index | Solver-side index |
| Armature: Bone Count | Total joints |
| Armature: Root Bone | Hierarchy root; the Transform that moves the whole rig |

The Animator writes poses into the armature; SkinnedMeshRenderer and PhysBone read them back. Keep bone names stable across reimports — retargeting, prefab overrides and animation events all key on names.

Workflow: import rig → verify Bone Count matches the DCC rig → set Root Bone to the hips/pelvis joint → bind SkinnedMeshRenderer → attach PhysBone chains (hair, coat) to the same armature → animate.

Troubleshooting: partial animation → stray bones outside the count, check DCC export; sliding feet → root motion on the wrong joint, reassign Root Bone; prefab override spam → bone renamed upstream, restore the name.

Related: Skinned Mesh Renderer, PhysBone, Animation Clips and Animator, Blend Shapes.
