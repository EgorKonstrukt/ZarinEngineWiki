# Gaussian Splat Renderer

Renders Gaussian splat captures — photogrammetry-style scenes as sorted point volumes.

| Field | Meaning |
|---|---|
| Splat Path | Capture file |
| SH Degree | Spherical-harmonics quality level |
| Opacity Cutoff | Transparency threshold |

Splats sort every frame through a dedicated fast path and share scene visibility and stats with meshes, but bypass parts of the PostFX chain — verify the graded look in the game view.

Collision for splat volumes uses the GS Volume Collider.

Related: GS Volume Collider, Materials, Cameras.
