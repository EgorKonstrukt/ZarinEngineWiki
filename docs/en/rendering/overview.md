# Rendering Overview

ZarinEngine renders with ModernGL on an OpenGL 4.6 core profile context. All engine math runs in `numpy.float64`; the downgrade to `float32` happens only when vertex data is uploaded to the GPU, which removes floating-point jitter in large worlds.

Building blocks:

- Custom `.shader` files with a `Properties { }` block (Unity ShaderLab style) plus GLSL vertex/fragment code.
- Material assets (`.zpem` / `.mat`, JSON) binding a shader with concrete property values.
- `MeshFilter` (which mesh) + `MeshRenderer` (how to draw it) on entities.
- Cameras (perspective / orthographic), lights (directional / point / spot / area) with shadows.
- Sprite, SVG, text and video renderers for 2D and UI-plane content.
- Particle systems, skybox, fog, a PostFX stack of 30+ effects, object effects, GPU skinning.
- Editor-only gizmo, icon and outline passes that never leak into the game view.

Subpages: pipeline and contexts, cameras, lights and shadows, materials, shaders, mesh rendering, sprite/SVG/text/video, particles, skybox and fog, PostFX, object effects, skinning, splats/raytracing/voxel, gizmos.
