# Воксель
> Воксельный вид: объект-эффект `VoxelizeEffect` (`core/components/rendering/effects/voxelize_effect.py:17-152`), рисует `VoxelPassMixin` (`core/renderer/voxel_pass.py`), compute `core/shaders/legacy/voxelize.comp`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
CPU-фолбэк `compute_voxel_instances` (`effects/voxel_cpu.py:50`); GPU-путь через `voxelize.comp` при наличии, иначе CPU-клетки на меш (`voxel_pass.py:218-221`). Лимиты: `_VOX_MAX_DIM = 128`, `_VOX_MAX_INSTANCES = 2200000` (`voxel_pass.py:22-25`). `amount` смешивает базу→воксели; `animate` (+`ping_pong`) крутит `amount` как `t % 1` треугольник (`voxelize_effect.py:78-86`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `amount` | слайдер (0–1) | `0.5` | Смешивание вокселизации (`voxelize_effect.py:138,36`). |
| `show_base_mesh` | bool | `True` | Оставить базу (`voxelize_effect.py:139,37`). |
| `world_grid` | bool | `False` | Привязка к мировой сетке (`voxelize_effect.py:140,38`). |
| `voxel_size` | float | `0.3` | Размер клетки (`voxelize_effect.py:141,39`). |
| `scale` | vec3 | `[1, 1, 1]` | XYZ-масштаб вокселя (`voxelize_effect.py:142,40`). |
| `jitter` | float | `0.0` | Джиттер клеток (`voxelize_effect.py:143,41`). |
| `emission` | float | `2.5` | Свечение (`voxelize_effect.py:144,42`). |
| `color` | color3 | `[0.4, 1.0, 0.6]` | Цвет вокселей (`voxelize_effect.py:145,43`). |
| `rim` | float | `2.5` | Степень френеля (`voxelize_effect.py:146,44`). |
| `cell` | float | `0.3` | Размер отрисовки клетки (`voxelize_effect.py:147,45`). |
| `double_sided` | bool | `True` | Без отсечения (`voxelize_effect.py:148,46`). |
| `animate` | bool | `False` | Анимировать amount (`voxelize_effect.py:149,47`). |
| `speed_anim` | float | `0.5` | Темп (`voxelize_effect.py:150,48`). |
| `ping_pong` | bool | `False` | Треугольник vs пила (`voxelize_effect.py:151,49`). |
## Usage in Editor
Voxelize Effect на сущность меша, Voxel Size выше — крупнее кубы, World Grid — общее выравнивание объектов в стиле Minecraft.
## Usage in Code
```python
vx.voxel_size = 0.2
vx.amount = 1.0
vx.animate = True
vx.ping_pong = True
```
## Related pages
- [Mesh Renderer](./mesh-renderer.md), [Pipeline](./pipeline.md)
## Verified against
- `core/components/rendering/effects/voxelize_effect.py:34-152`
- `core/renderer/voxel_pass.py:22-63,199-258`
- `core/shaders/legacy/voxelize.comp`
