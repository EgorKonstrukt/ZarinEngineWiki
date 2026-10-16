# Гауссов сплат-рендерер
> Билборды 3DGS: компонент `GaussianSplatRenderer` (`core/components/rendering/renderers/gaussian_splat_renderer.py`) + GL-бэкенд (`core/renderer/splat/renderer.py`, `resources.py`, `sort.py`, `volume.py`).
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Загрузка `.ply / .sog / .ssog` (фильтр, `gaussian_splat_renderer.py:23-24`; загрузчики `ply_loader.py:225`, `sog/loader.py:259`, `sog/async_ops.py`). Бэкенду нужен OpenGL 4.3+ (иначе ошибка, `splat/resources.py:343`). Сортировка CPU/Cython в `splat/sort.py` (расширение `_splat_sort`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `ply_path` | путь (ply/sog/ssog) | `""` | Файл сплата (`gaussian_splat_renderer.py:23,33`). |
| `sh_degree` | int слайдер (0–3) | `3` | Степень сферических гармоник (`gaussian_splat_renderer.py:25,34`). |
| `opacity_threshold` | float (0–1) | `0.005` | Отсечение по опасити (`gaussian_splat_renderer.py:27,35`). |
## Usage in Editor
Add Component → Gaussian Splat Renderer, назначить `.ply`, SH 3 для качества / 0–1 для скорости, cutoff выше — режет шум.
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
