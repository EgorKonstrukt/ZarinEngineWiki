# Проектор
> Проектор текстуры: компонент `Projector` (`core/components/lighting/projector.py`) бросает картинку вдоль forward сущности, как слайд-проектор.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Перспективный слайд задаётся `spot_angle`, `aspect_ratio`, `near_plane`/`far_plane`; `range` ограничивает дальность броска. Гизмо рисует фрустум + средний спрайт-квад (`projector.py:52-106`). Хук теней: `render_projector_shadows` (`core/renderer/shadows/projector.py:28`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `texture_path` | путь (png/jpg/jpeg) | `""` | Слайд (`projector.py:22,40`). |
| `color` | color3 | `[1.0, 1.0, 1.0]` | Множитель-тинт (`projector.py:23,41`). |
| `intensity` | float (0–1000) | `1.0` | Яркость (`projector.py:24,42`). |
| `range` | float (0–10000) | `10.0` | Дальность броска (`projector.py:26,43`). |
| `spot_angle` | float (1–179) | `30.0` | Вертикальный FOV, град (`projector.py:27,44`). |
| `aspect_ratio` | float (0.1–10) | `1.0` | Отношение сторон (`projector.py:28,45`). |
| `near_plane` | float (0.01–10) | `0.1` | Ближний клип (`projector.py:29,46`). |
| `far_plane` | float (1–10000) | `100.0` | Дальний клип (`projector.py:30,47`). |
| `flip_y` | bool | `True` | Отразить по вертикали (`projector.py:32,48`). |
| `flip_x` | bool | `False` | Отразить по горизонтали (`projector.py:33,49`). |
| `cast_shadows` | bool | `True` | Теневая маска (`projector.py:34,50`). |
## Usage in Editor
Add Component → Projector, назначить Texture, навести трансформом. Aspect держать равным исходнику; Flip Y включать для камерных слайдов.
## Usage in Code
```python
pj.texture_path = "assets/textures/logo.png"
pj.spot_angle = 40.0
pj.aspect_ratio = 16.0 / 9.0
pj.range = 20.0
```
## Related pages
- [Spot Light](./spot-light.md), [Shadows](./shadows.md), [Materials](./materials.md)
## Verified against
- `core/components/lighting/projector.py:18-50,108-140`
- `core/renderer/shadows/projector.py:28`
