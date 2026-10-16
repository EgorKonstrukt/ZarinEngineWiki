# Силовое поле частиц
> Влиятель на частицы: `ParticleForceField` (`core/components/rendering/particles/particle_force_field.py`); максимум `16` полей в SSBO (`MAX_FORCE_FIELDS = 16`).
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`ForceFieldShape`: `sphere / box` (`particle_force_field.py:29-31`). `to_gpu_data()` пакует позицию+радиус, силу+интенсивность, полуоси+флаг формы, extras (start_range/drag/gravity/флаг дистанции) (`particle_force_field.py:75-103`). Гизмо рисует кольца сферы или рёбра бокса + внутреннюю клетку start-range (`particle_force_field.py:105-168`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `shape` | `ForceFieldShape` | `SPHERE` | Объём влияния (`particle_force_field.py:46,62`). |
| `radius` | float (0.01–500) | `5.0` | Радиус сферы (`particle_force_field.py:47,63`). |
| `box_size` | vec3 (0.01–500) | `[5.0, 5.0, 5.0]` | Габариты бокса (`particle_force_field.py:48,64`). |
| `start_range` | float (0–500) | `0.0` | Внутренняя мёртвая зона (`particle_force_field.py:49,65`). |
| `force_x` / `force_y` / `force_z` | float (−1000…1000) | `0.0` / `1.0` / `0.0` | Вектор силы (`particle_force_field.py:51-53,66-68`). |
| `intensity` | float (0–100) | `1.0` | Масштаб силы (`particle_force_field.py:54,69`). |
| `multiply_by_distance` | bool | `True` | Масштаб по дистанции (`particle_force_field.py:55,70`). |
| `drag` | float (0–100) | `0.0` | Демпфирование (`particle_force_field.py:56,71`). |
| `gravity` | float (−100…100) | `0.0` | Доп. гравитация (`particle_force_field.py:57,72`). |
## Usage in Editor
Одно на сущность (`_allow_multiple = False`). Sphere для взрывов/потоков, Box для аэродинамических труб; Start Range выдалбливает ядро.
## Usage in Code
```python
ff.shape = ForceFieldShape.BOX
ff.box_size = [10.0, 4.0, 10.0]
ff.force_y = 3.0
ff.drag = 0.2
```
## Related pages
- [Particle System](./particle-system.md)
## Verified against
- `core/components/rendering/particles/particle_force_field.py:17-103,170-202`
