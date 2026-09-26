# Gaussian Splat Renderer

Renders Gaussian splat captures — photogrammetry-style scenes as sorted point volumes with spherical-harmonics color.

| Field | Meaning |
|---|---|
| Splat Path | Capture file |
| SH Degree | Spherical-harmonics quality level (higher = richer view-dependent color, more memory) |
| Opacity Cutoff | Transparency threshold culling faint splats |

Splats sort every frame through a dedicated fast path (`_splat_sort` accelerated module) and share scene visibility and stats with meshes, but bypass parts of the PostFX chain — verify the graded look in the game view, not just the editor.

Collision for splat volumes uses the GS Volume Collider (voxelized box approximation from the same capture).

Workflows: captured courtyard — splat entity for visuals plus GS Volume Collider for walking; hybrid scenes — splats for background detail, meshes for interactive foreground; performance — lower SH Degree first, then opacity cutoff, then capture density.

Troubleshooting: washed colors → SH Degree too low for the lighting variance; holes → opacity cutoff too aggressive; no collision → collider missing or voxel size too coarse.

Related: GS Volume Collider, Voxel Rendering, Cameras, Materials.
