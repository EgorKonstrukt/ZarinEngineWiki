# Raytracing Renderer

Experimental raytraced view component for pipeline research and reference renders — not a production path (the engine targets OpenGL 4.6, no Vulkan/DX12).

| Field | Meaning |
|---|---|
| Enabled | Master switch |
| Compute Shader | Raytrace kernel entry |
| Resolution Scale | Render resolution multiplier (0.25 for drafts, 1.0 for finals) |
| Max Bounces | Path depth per ray |
| Samples Per Pixel | Quality vs noise per frame |
| Accumulate Frames | Temporal convergence across frames (still camera required) |
| Show Overlay / Heatmap (+ Mode / Scale) | Cost visualization for research |

Use for lighting studies (compare against the rasterized frame), material validation and marketing stills. Ship games on the standard mesh pipeline.

Accumulation converges only while the camera and scene are static — any motion restarts convergence, which is expected behavior, not a bug.

Troubleshooting: noise never clears → motion or accumulation off; black → compute shader failed, check console; editor slowdown → resolution scale 1.0 with high SPP, drop to draft settings.

Related: Gaussian Splat Renderer, Cameras, Profiler.
