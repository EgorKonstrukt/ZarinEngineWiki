# Terrain Collider

Heightfield collision following terrain data — cheaper and more stable than a mesh collider for landscapes.

| Inspector group / field | Meaning |
|---|---|
| Collision: Layer / Collision Mask | Filtering |
| Terrain: World Size | XZ extent of the heightfield |
| Terrain: Height Scale | Vertical exaggeration |
| Terrain: Resolution | Height sample density |
| Terrain: Center | Local offset |
| Is Trigger | Overlap events without contact response |
| Material: Physic Material | `.zphysmat` asset override |

Regenerate the collider after sculpting the terrain, or the physics surface will disagree with the visible one.

Related: Terrain (rendering section), Rigidbody, Mesh Collider.
