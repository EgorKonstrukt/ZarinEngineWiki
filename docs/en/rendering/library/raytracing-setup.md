# Raytracing Setup

> `Raytracing.compute` kernel contract, component knobs, hard limits, and performance guidance.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

The path-tracer is a `#version 430 core` compute kernel (`local_size 8×8`, `core/shaders/compute/Raytracing.compute:13-15`) driven by the `RaytraceRenderer` component split across `core/components/rendering/renderers/raytrace/` (`component.py`, `dispatch.py`, `denoise.py`).

## Kernel properties (verbatim, `Raytracing.compute:3-11`)

```c
Properties
{
    _MaxBounces("Max Bounces", Int) = 1
    _SamplesPerPixel("Samples Per Pixel", Int) = 1
    _ResolutionScale("Resolution Scale", Float) = 1.0
    _Accumulate("Accumulate Frames", Int) = 0
    _HeatmapMode("Heatmap Mode", Int) = 0
    _HeatmapScale("Heatmap Scale", Float) = 40.0
}
```

Hard GLSL limits (`Raytracing.compute:17-18`): `#define MAX_INSTANCES 256`, `#define MAX_LIGHTS 8`. Extra instances/lights are dropped during scene upload (`denoise.py:85,217-243`).

## Component knobs (`raytrace/component.py:31-49`)

| field | range | default | description |
|---|---|---|---|
| `_resolution_scale` | 0.1–1.0 | `0.5` | Trace resolution factor; dispatch size `max(1, size × scale)` (`dispatch.py:17-18`). |
| `_max_bounces` | 1–16 | `1` | → `u_max_bounces`. |
| `_samples_per_pixel` | 1–16 | `1` | → `u_samples_per_pixel`. |
| `_accumulate` | bool | `False` | → `u_accumulate`; progressive refine (`dispatch.py:126`). |
| `_show_overlay` | bool | `True` | Composite overlay presentation. |
| `_heatmap_mode` | 0–2 | `0` | → `u_heatmap_mode` (off/cost diagnostic). |
| `_heatmap_scale` | 1–256 | `40.0` | → `u_heatmap_scale`. |

Buffers bound at dispatch: BVH nodes (0), vertices (1), indices (2), materials (3), instances (4), lights (5), plus `rgba32f` output/emissive images and a 32-layer albedo texture array (`_MAX_ALBEDO_LAYERS = 32`, `component.py:17`, `dispatch.py:79`).

## Requirements

- OpenGL 4.3+ compute (SSBO + image load/store, `#version 430 core`).
- Scene caps: 256 instances, 8 lights, 32 albedo layers — components beyond the cap are ignored with no error besides the drop.
- Rays/frame accounting: `rw × rh × spp × (bounces + 1)` (`dispatch.py:271`, surfaced as `_rt_rays_per_frame`).

## Performance notes

Cost scales linearly with pixels (× `resolution_scale²`), spp, and bounces+1. Start at `0.5` scale / 1 bounce / 1 spp, enable `_accumulate` for stills, and use `_heatmap_mode` to find hot spots before raising quality.

## Usage in Code

```python
rt._resolution_scale = 0.5
rt._max_bounces = 1
rt._samples_per_pixel = 1
rt._accumulate = False
```

## Related pages

- [Raytracing Renderer](../raytracing-renderer.md), [Shader Catalog](./shader-catalog.md), [Pipeline](../pipeline.md)

## Verified against

- `core/shaders/compute/Raytracing.compute:1-60` (Properties, `#version 430`, `local_size 8×8`, `MAX_INSTANCES 256`, `MAX_LIGHTS 8`, uniforms)
- `core/components/rendering/renderers/raytrace/component.py:14-49,95-121` (limits, knobs, defaults, serialization)
- `core/components/rendering/renderers/raytrace/dispatch.py:17-18,124-160,271-294` (scale, uniforms, ray count, overlay)
- `core/components/rendering/renderers/raytrace/denoise.py:85,217-260` (cap enforcement)
