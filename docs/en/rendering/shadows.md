# Shadows
> Shadow-map system: `ShadowRenderer` (`core/renderer/shadows/renderer.py`, `passes.py:183`) plus per-type quality owners `DirectionalShadow / PointShadow / SpotShadow / AreaShadow`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`render_shadow_pass(...)` renders `renderable_shadow` casters into per-light maps: CSM for directional (`shadows/csm.py`), cube/dual maps for point (`shadows/point_spot.py`), perspective for spot, multi-sample for area (`shadows/area.py`), cookies for [Projector](./projector.md) (`shadows/projector.py`). Without an active owner component of a type, that type's shadows are disabled (headers in `directional_shadow.py:7-9`, `point_shadow.py:7-9`, `spot_shadow.py:7-9`, `area_shadow.py:7-9`). Per-receiver flags: `MeshRenderer.cast_shadows / receive_shadows`.
## Properties
| name | type | default | description |
|---|---|---|---|
| `DirectionalShadow._shadow_resolution` | int slider 256–4096 | `4096` | Sun map size (`directional_shadow.py:33,45`). |
| `DirectionalShadow._shadow_distance` | float slider 1–500 | `50.0` | CSM range (`directional_shadow.py:35,46`). |
| `DirectionalShadow._cascade_count` | int slider 1–4 | `4` | Cascade count (`directional_shadow.py:37,58`). |
| `DirectionalShadow._cascade_splits` | split gradient | `[0.05, 0.13, 0.3]` | Cascade boundaries for 4 cascades (`directional_shadow.py:18,39,47`). |
| `PointShadow._shadow_resolution` | int slider 128–2048 | `512` | Point map size (`point_shadow.py:31,37`). |
| `SpotShadow._shadow_resolution` | int slider 128–2048 | `1024` | Spot map size (`spot_shadow.py:31,37`). |
| `AreaShadow._shadow_resolution` | int slider 128–2048 | `512` | Area map size (`area_shadow.py:31,37`). |
| `Light.cast_shadows` | bool | `True` | Per-light toggle (see light pages). |
## Usage in Editor
Add exactly one owner per type you use (e.g. Directional Shadow). Raise Resolution for crisp edges, shorten Shadow Distance to concentrate CSM texels, use 2–3 cascades for small scenes and 4 for open worlds.
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
