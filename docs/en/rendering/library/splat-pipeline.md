# Splat Pipeline

> Gaussian splat ingest → GPU upload → sorted rasterization: supported formats, SH degree, opacity cutoff, GL requirements, and the `_splat_sort` sorter.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

`GaussianSplatRenderer` (`core/components/rendering/renderers/gaussian_splat_renderer.py:12-35`) points at a splat file and tunes evaluation. The GPU backend is `_SplatResourcesMixin` (`core/renderer/splat/resources.py:330-388`); sorting uses the optional Cython `core._splat_sort` module (`core/renderer/splat/sort.py:31-34`, built in `core/build_cython.py:127`).

## Properties

| name | type | default | description |
|---|---|---|---|
| `ply_path` | resource path | `""` | Splat file; picker filter `Splats (*.ply *.sog *.ssog)` (`gaussian_splat_renderer.py:23-24`). |
| `sh_degree` | int slider 0–3 | `3` | Spherical-harmonics degree uploaded as `u_sh_degree` (`gaussian_splat_renderer.py:25-26`, `resources.py:385-386`). |
| `opacity_threshold` | float 0–1 | `0.005` | Opacity cutoff uploaded as `u_opacity_threshold` (`gaussian_splat_renderer.py:27-28`, `resources.py:387-388`). |

## Formats and import

`_is_sog_style` (`resources.py:306-313`) treats these as directory/archive-style splats: `.sog`, `.ssog`, `.zip`, paths ending in `/meta.json` or `/lod-meta.json`, or basenames `meta.json` / `lod-meta.json`. Everything else goes through `_load_splat_file` (`resources.py:316-327`): the first 4 lines are sniffed — `binary` PLY takes the fast path (`_load_splat_fast`), otherwise the generic loader (`_load_splat_generic`). Relative `ply_path` values resolve against the project root with an `assets/` fallback (`post_pass.py:47-61`).

## GPU requirements and sorting

- Shader init failure logs `GaussianSplatRenderer shader init failed (needs OpenGL 4.3+): ...` and disables the program/VAO (`resources.py:342-345`). SSBO storage buffers are used (`_ensure_buffers`, `_bind_gpu_draw` binding points 0–1).
- Per-frame uniforms: `u_model`, `u_view`, `u_proj`, `u_viewport`, `u_camera_pos`, `u_sh_degree`, `u_opacity_threshold` (`resources.py:369-388`).
- Sorting uses `splat_cull_depth`, `compact_keep`, `radix_sort_into`, `remap_order` from `core._splat_sort` when compiled, with a NumPy fallback (`splat/sort.py:31-34,168`).

## Usage in Code

```python
from core.components.rendering.renderers.gaussian_splat_renderer import GaussianSplatRenderer
gs = GaussianSplatRenderer()
gs.ply_path = "assets/splats/scan.ply"
gs.sh_degree = 3
gs.opacity_threshold = 0.005
entity.add_component(gs)
```

## Related pages

- [Gaussian Splat Renderer](../gaussian-splat-renderer.md), [Pipeline](../pipeline.md)

## Verified against

- `core/components/rendering/renderers/gaussian_splat_renderer.py:19-53` (fields, defaults, serialization)
- `core/renderer/splat/resources.py:300-388` (SOG detection, loader dispatch, GL 4.3+ message at `:343`, SSBO buffers, uniforms)
- `core/renderer/splat/sort.py:31-34`, `core/build_cython.py:127` (`core._splat_sort`)
- `core/renderer/post_pass.py:47-61` (`_gaussian_ply_path` resolution)
