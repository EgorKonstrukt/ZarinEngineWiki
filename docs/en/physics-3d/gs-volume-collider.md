# GS Volume Collider

Collision volumes derived from Gaussian splat captures.

| Inspector group / field | Meaning |
|---|---|
| Gs Volume Collider: Splat Path | Source capture |
| Voxel Size | Volume discretization step |
| Opacity Cutoff | Density threshold |
| Dilation | Volume expansion in voxels |
| Max Boxes | Box-approximation budget |
| Center | Local offset |
| Layer / Collision Mask / Is Trigger | Standard filtering |
| Physic Material | `.zphysmat` asset override |

Lets players walk on and bump into photogrammetry-style splat scenes that have no mesh geometry.

Related: Gaussian Splat Renderer, Box Collider, Mesh Collider.
