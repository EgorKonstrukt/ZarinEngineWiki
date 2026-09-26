# Rendering Overview

ZarinEngine renders with ModernGL on an OpenGL 4.6 core profile context. All engine math runs in `numpy.float64`; the downgrade to `float32` happens only when vertex data is uploaded to the GPU, which removes floating-point jitter in large worlds — coordinates stay exact across kilometers of scenery while the rasterizer still gets the floats it wants.

Building blocks, bottom to top:

- Custom `.shader` files with a `Properties { }` block (Unity ShaderLab style) plus GLSL vertex/fragment code. The loader compiles, falls back across SubShaders, injects instancing and UV overrides, and retries failed 430 shaders as 330.
- Material assets (`.zpem` / `.mat`, JSON) binding a shader with concrete property values, editable live in the Inspector and the material wizard.
- `MeshFilter` (which mesh) + `MeshRenderer` (how to draw it) on entities; `SkinnedMeshRenderer` + `Armature` for animation; `MeshCollider` reuses the same import for physics.
- Cameras (perspective / orthographic, depth ordering, render scale), lights (directional / point / spot / area, physical units) with cascaded, cube and spot shadow maps.
- Sprite, SVG, text and video renderers for world-space 2D content; the GUI system (`GuiCanvas` + 30+ widgets) for screen-space UI.
- Particle systems with editor-visible gizmo shapes, force fields, GPU-driven playback for heavy effects.
- Environment set: procedural sky coupled to the sun, HDRI skybox, clouds, water, atmosphere scattering.
- A PostFX stack of 31 effects plus 11 per-object effects, GPU skinning, Gaussian splats, an experimental raytracing view, voxel paths.
- Editor-only gizmo, icon, outline and grid passes that never leak into builds.

Frame budget thinking: draw calls scale with visible submeshes, shadow passes redraw the scene per cascade/face, transparent overdraw and multi-tap post effects dominate fill rate. The built-in profiler breaks every stage down (`render_scene`, shadow timings, `postfx`, `gizmo_lines`) — profile before optimizing blindly.

Subpages: pipeline and contexts, cameras, lights and shadows, mesh rendering and skinning, sprites and video, particles, environment, materials, shaders, PostFX, object effects, splats and voxel, gizmos.
