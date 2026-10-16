# Тени
> Система карт теней: `ShadowRenderer` (`core/renderer/shadows/renderer.py`, `passes.py:183`) плюс владельцы качества `DirectionalShadow / PointShadow / SpotShadow / AreaShadow`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`render_shadow_pass(...)` рисует кастеры `renderable_shadow` в карты каждого света: CSM для directional (`shadows/csm.py`), куб/двойные для point (`shadows/point_spot.py`), перспективная для spot, мультисэмпл для area (`shadows/area.py`), куки для [Projector](./projector.md) (`shadows/projector.py`). Без активного владельца типа тени этого типа выключены (шапки `directional_shadow.py:7-9`, `point_shadow.py:7-9`, `spot_shadow.py:7-9`, `area_shadow.py:7-9`). Флаги на приёмнике: `MeshRenderer.cast_shadows / receive_shadows`.
## Properties
| name | type | default | description |
|---|---|---|---|
| `DirectionalShadow._shadow_resolution` | int слайдер 256–4096 | `4096` | Размер карты солнца (`directional_shadow.py:33,45`). |
| `DirectionalShadow._shadow_distance` | float слайдер 1–500 | `50.0` | Дальность CSM (`directional_shadow.py:35,46`). |
| `DirectionalShadow._cascade_count` | int слайдер 1–4 | `4` | Число каскадов (`directional_shadow.py:37,58`). |
| `DirectionalShadow._cascade_splits` | split gradient | `[0.05, 0.13, 0.3]` | Границы каскадов для 4 (`directional_shadow.py:18,39,47`). |
| `PointShadow._shadow_resolution` | int слайдер 128–2048 | `512` | Размер point-карты (`point_shadow.py:31,37`). |
| `SpotShadow._shadow_resolution` | int слайдер 128–2048 | `1024` | Размер spot-карты (`spot_shadow.py:31,37`). |
| `AreaShadow._shadow_resolution` | int слайдер 128–2048 | `512` | Размер area-карты (`area_shadow.py:31,37`). |
| `Light.cast_shadows` | bool | `True` | Тумблер на свете (см. страницы света). |
## Usage in Editor
Один владелец на используемый тип (напр. Directional Shadow). Resolution выше — чётче края, Shadow Distance короче — плотнее тексели CSM, каскадов 2–3 для малых сцен и 4 для открытых.
## Usage in Code
```python
ds = entity.get_component(DirectionalShadow)
ds._shadow_resolution = 2048
ds._shadow_distance = 120.0
```
## Related pages
- [Light](./light.md), [Directional Light](./directional-light.md), [Point Light](./point-light.md), [Spot Light](./spot-light.md), [Area Light](./area-light.md), [Mesh Renderer](./mesh-renderer.md)
## Verified against
- `core/renderer/shadows/passes.py:59,183`
- `core/renderer/shadows/csm.py`, `point_spot.py`, `area.py`, `projector.py:28`
- `core/components/rendering/environment/directional_shadow.py:28-48`
- `core/components/rendering/environment/point_shadow.py:26-37`
- `core/components/rendering/environment/spot_shadow.py:26-37`
- `core/components/rendering/environment/area_shadow.py:26-37`
