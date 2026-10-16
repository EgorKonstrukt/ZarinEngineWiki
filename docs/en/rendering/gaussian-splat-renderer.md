# Gaussian Splat Renderer
> 3DGS billboards: `GaussianSplatRenderer` component (`core/components/rendering/renderers/gaussian_splat_renderer.py`) + GL backend (`core/renderer/splat/renderer.py`, `resources.py`, `sort.py`, `volume.py`).
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Loads `.ply / .sog / .ssog` (`file_filter`, `gaussian_splat_renderer.py:23-24`; loaders `ply_loader.py:225`, `sog/loader.py:259`, `sog/async_ops.py`). Backend needs OpenGL 4.3+ (error otherwise, `splat/resources.py:343`). CPU/Cython sort in `splat/sort.py` (`_splat_sort` extension).
## Properties
| name | type | default | description |
|---|---|---|---|
| `ply_path` | resource path (ply/sog/ssog) | `""` | Splat file (`gaussian_splat_renderer.py:23,33`). |
| `sh_degree` | int slider (0–3) | `3` | Spherical-harmonics degree (`gaussian_splat_renderer.py:25,34`). |
| `opacity_threshold` | float (0–1) | `0.005` | Opacity cutoff (`gaussian_splat_renderer.py:27,35`). |
## Usage in Editor
Add Component → Gaussian Splat Renderer, assign a `.ply`, keep SH degree 3 for quality / 0–1 for perf, raise cutoff to cull noise.
## Usage in Code
```python
gs = entity.get_component(GaussianSplatRenderer)
gs.ply_path = "assets/splats/garden.ply"
gs.sh_degree = 2
gs.opacity_threshold = 0.01
```
`render(path, model, view, proj, cam_pos, w, h, ...)` (`splat/renderer.py:362`).
## Related pages
- [Mesh Renderer](./mesh-renderer.md), [Pipeline](./pipeline.md)
## Verified against
- `core/components/rendering/renderers/gaussian_splat_renderer.py:19-53`
- `core/renderer/splat/renderer.py:362`, `resources.py:343`, `sort.py`, `volume.py`
