# GS Volume Collider

Collision volumes derived from Gaussian splat captures, so players walk on photogrammetry-style scenes that have no mesh geometry at all.

| Inspector group / field | Meaning |
|---|---|
| Gs Volume Collider: Splat Path | Source capture (same file as the renderer) |
| Voxel Size | Volume discretization step — fidelity knob |
| Opacity Cutoff | Density threshold: below it is air |
| Dilation | Volume expansion in voxels (safety skin) |
| Max Boxes | Box-approximation budget for the solver |
| Center | Local offset |
| Layer / Collision Mask / Is Trigger | Standard filtering |
| Physic Material | `.zphysmat` asset override |

How it works: the capture voxelizes at Voxel Size, cells above Opacity Cutoff survive, Dilation grows a safety skin, and the solid set approximates into at most Max Boxes solver shapes. Finer voxels hug statues; coarser voxels run faster.

Tuning order: voxel size to the smallest gameplay feature (steps need fine voxels), cutoff until floating dust stops colliding, dilation 1–2 for forgiveness, max boxes until the gizmo covers walkable areas.

Static by nature — pair with the GaussianSplatRenderer visuals, keep dynamic gameplay props as meshes with primitive colliders on top.

Troubleshooting: falls through statues → voxels too coarse or cutoff too high; collides with air → cutoff too low catching haze, raise it; blocky invisible walls → dilation overshot, lower; slow cook → voxel size tiny over a huge capture, coarsen.

Related: Gaussian Splat Renderer, Box Collider, Mesh Collider, Voxel Rendering.
