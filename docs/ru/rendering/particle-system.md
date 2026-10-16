# Система частиц
> GPU-эмиттер: `ParticleSystem` (`core/components/rendering/particles/partic/__init__.py`, поля в `partic/component.py:56-101`), считает/рисует `core/renderer/particles.py`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Энамы: `ShapeType cone/sphere/hemisphere/box/circle`, `ParticleRenderMode billboard/stretched_billboard`, `SimulationSpace local/world`, `ParticleSortMode none/by_distance/by_age` (`component.py:16-34`). Хранилище `PARTICLE_DTYPE` (position/velocity/color/meta/lifetime/start_color/start_meta). Эмиссия по rate/distance + bursts, кривые size/color/rotation/velocity-over-lifetime.
## Properties
| name | type | default | description |
|---|---|---|---|
| `duration` | float (0.01–300) | `5.0` | Длительность эмиссии (`component.py:60,105`). |
| `looping` | bool | `True` | Зацикливание (`component.py:61,106`). |
| `prewarm` | bool | `False` | Досчёт до первого кадра (`component.py:62,107`). |
| `start_delay` | float (0–60) | `0.0` | Задержка, с (`component.py:63,108`). |
| `start_lifetime` | float (0.01–300) | `5.0` | Базовое время жизни (`component.py:65,109`). |
| `start_lifetime_min` / `start_lifetime_max` | float | `0.0` / `0.0` | Случайный разброс (`component.py:66-67`). |
| `start_speed` | float (0–1000) | `5.0` | Базовая скорость (`component.py:68,112`). |
| `start_speed_min` / `start_speed_max` | float | `0.0` / `0.0` | Разброс (`component.py:69-70`). |
| `start_size` | float (0–100) | `1.0` | Базовый размер (`component.py:71,115`). |
| `start_size_min` / `start_size_max` | float | `0.0` / `0.0` | Разброс (`component.py:72-73`). |
| `start_rotation` | float (0–360) | `0.0` | Начальный поворот (`component.py:74,119`). |
| `flip_rotation` | float (0–360) | `0.0` | Зеркальный поворот (`component.py:75,120`). |
| `inherit_velocity` | float (0–1) | `0.0` | Доля скорости эмиттера (`component.py:76,121`). |
| `gravity_modifier` | float (0–10) | `0.0` | Масштаб гравитации (`component.py:77,122`). |
| `simulation_space` | `SimulationSpace` | `LOCAL` | Local/world (`component.py:79,123`). |
| `max_particles` | int (1–10000000) | `1000` | Лимит пула (`component.py:80,124`). |
| `rate_over_time` | float (0–100000) | `10.0` | Частиц/с (`component.py:81,125`). |
| `shape_type` | `ShapeType` | `CONE` | Форма эмиттера (`component.py:82,128`). |
| `shape_angle` | float (0–360) | `25.0` | Угол конуса (`component.py:83,129`). |
| `shape_radius` | float (0–100) | `1.0` | Радиус (`component.py:84,130`). |
| `render_mode` | `ParticleRenderMode` | `BILLBOARD` | Режим билборда (`component.py:86,134`). |
| `material_path` | путь (*.mat) | `""` | Материал частиц (`component.py:87,135`). |
| `sort_mode` | `ParticleSortMode` | `NONE` | Сортировка (`component.py:88,137`). |
| `sorting_fudge` | float (−100…100) | `0.0` | Сдвиг сортировки (`component.py:89,138`). |
| `flipbook_columns` / `flipbook_rows` | int (1–100) | `1` / `1` | Сетка спрайт-шита (`component.py:91-92`). |
| `flipbook_fps` | float (0–120) | `0.0` | FPS шита (`component.py:93,141`). |
| `size_over_lifetime_enabled` | bool | `False` | Тумблер кривой размера (`component.py:95,142`). |
| `color_over_lifetime_enabled` | bool | `False` | Тумблер градиента цвета (`component.py:97,146`). |
| `rotation_over_lifetime_enabled` | bool | `False` | Тумблер вращения (`component.py:99,148`). |
| `velocity_over_lifetime_enabled` | bool | `False` | Тумблер сил (`component.py:100,150`). |
## Usage in Editor
Max Particles + Rate, форма Shape, материал Material. Size/Color over Lifetime для затуханий; Flipbook для анимированных спрайтов. Комбинировать с [Particle Force Field](./particle-force-field.md).
## Usage in Code
```python
ps.rate_over_time = 60.0
ps.shape_type = ShapeType.SPHERE
ps.shape_radius = 2.0
ps.color_over_lifetime_enabled = True
```
## Related pages
- [Particle Force Field](./particle-force-field.md), [Materials](./materials.md)
## Verified against
- `core/components/rendering/particles/partic/component.py:16-141`
- `core/renderer/particles.py:312`
