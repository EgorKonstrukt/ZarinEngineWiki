# Terrain Collider

Heightfield collision following terrain data: the only sane way to collide with landscapes — smooth height sampling instead of millions of triangles.

| Inspector group / field | Meaning |
|---|---|
| Collision: Layer / Collision Mask | Filtering |
| Terrain: World Size | XZ extent of the heightfield in world units |
| Terrain: Height Scale | Vertical exaggeration multiplier |
| Terrain: Resolution | Height sample density (fidelity vs memory) |
| Terrain: Center | Local offset |
| Is Trigger | Overlap events without contact response |
| Material: Physic Material | `.zphysmat` asset override |

The collider must match the visual terrain: same world size, same height scale, same resolution family. Regenerate after every sculpt — the classic invisible-wall / floating-player bug is a stale heightfield disagreeing with the edited mesh.

Resolution guide: gameplay fidelity needs samples denser than the smallest feature players touch (curbs, ruts); distant mountains can stay coarse. Over-resolution wastes memory and cook time for zero gameplay gain.

Characters walk heightfields smoothly where mesh colliders would chatter on triangle edges — another reason terrain always pairs with this collider, never a mesh one.

Troubleshooting: sinking to the knees → height scale mismatch; floating → stale field, regenerate; edge cliffs at borders → world size smaller than the visual, extend; slow load → resolution overshot, halve it.

Related: Terrain (rendering section), Rigidbody, Mesh Collider, CharacterController (max slope interplay).
