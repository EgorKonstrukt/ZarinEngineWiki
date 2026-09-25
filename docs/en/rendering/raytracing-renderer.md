# Raytracing Renderer

Experimental raytraced view component for pipeline research — not a production path (the engine targets OpenGL 4.6, no Vulkan/DX12).

| Field | Meaning |
|---|---|
| Enabled | Master switch |
| Compute Shader | Raytrace kernel |
| Resolution Scale | Render resolution multiplier |
| Max Bounces | Path depth |
| Samples Per Pixel | Quality vs noise |
| Accumulate Frames | Temporal convergence |
| Show Overlay / Heatmap (+ Mode / Scale) | Cost visualization |

Useful for reference renders and lighting studies; ship games on the standard mesh pipeline.

Related: Gaussian Splat Renderer, Cameras, Profiler.
