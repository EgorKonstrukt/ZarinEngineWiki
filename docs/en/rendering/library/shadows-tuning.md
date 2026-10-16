# Shadows Tuning

> Resolution/distance/cascade defaults per light type, bias constants, and performance notes — all from code.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Shadow settings have two layers: the `DirectionalShadow` scene component (authored values) and renderer fallbacks (`core/renderer/renderer.py:125-131`) used when no component is present. The shadow backend is `ShadowRenderer` (`core/renderer/shadows/renderer.py:23-30`).

## Properties

| setting | authored default (`DirectionalShadow`) | renderer fallback | code |
|---|---|---|---|
| Directional resolution | `4096` | `2048` | `directional_shadow.py:45`, `renderer.py:125` |
| Shadow distance | `50.0` m | `50.0` m | `directional_shadow.py:46`, `renderer.py:126` |
| Cascades | `4` | `4` | `directional_shadow.py:48`, `renderer.py:127` |
| Cascade splits (4) | `[0.05, 0.13, 0.3]` | computed log/linear blend (`lam = 0.9`) | `directional_shadow.py:18`, `csm.py:69-75` |
| Point resolution | — | `512` | `renderer.py:129` |
| Area resolution | — | `512` | `renderer.py:130` |
| Spot resolution | — | `1024` | `renderer.py:131` |
| `u_shadow_bias` | — | `0.0015` | `shadows/uniforms.py:213` |
| `u_shadow_normal_bias` | — | `0.02` | `shadows/uniforms.py:214` |
| `area_shadow_bias` | `0.005` (per `AreaLight`) | `0.005` | `light.py:90`, `shadows/area.py:71-73` |

Cascade tile resolutions for a 4-cascade map derive from the base: `[base, max(512, base//2), max(512, base//2), max(256, base//4)]` (`shadows/uniforms.py:33`), with a static atlas layout of `[2048, 1024, 1024, 512]` in the renderer (`shadows/renderer.py:70`).

## Tuning guide

- **Directional**: resolution slider 256–4096 step 256, distance 1–500 m, cascades 1–4 (`directional_shadow.py:33-40`). 4096 + 4 cascades is the authored default; drop to 2048/2 cascades for low-end.
- **Splits**: defaults per count are `{2: [0.5], 3: [0.2, 0.6], 4: [0.05, 0.13, 0.3]}` (`directional_shadow.py:18`, mirrored in `editor/cascade_split_widget.py:15`). Push splits outward to cover far ground, inward for close-up detail.
- **Point/spot/area**: fixed at 512/1024/512 in the renderer; disable `cast_shadows` on fill lights instead of lowering globally.
- **Acne vs peter-panning**: raise `u_shadow_bias`/`u_shadow_normal_bias` against acne; raise `area_shadow_bias` (0–0.1, step 0.001) per area light (`shadows.py:100`).

## Performance notes

Shadow cost scales with cascade count × tile resolution plus one 512² cube-ish pass per point light and one 1024² pass per spot. `ShadowRenderer` clamps cascades to 1–4 (`shadows/renderer.py:25`). Outline queue rendering is capped at 512 entries (`output.py:397`).

## Usage in Code

```python
from core.components.rendering.environment.directional_shadow import DirectionalShadow
ds = DirectionalShadow()
ds._shadow_resolution = 4096
ds._shadow_distance = 50.0
ds._set_cascade_count(4)
```

## Related pages

- [Shadows](../shadows.md), [Directional Light](../directional-light.md), [Light Units](./light-units.md)

## Verified against

- `core/components/rendering/environment/directional_shadow.py:18-48` (splits table, 4096/50.0/4 defaults, sliders)
- `core/renderer/renderer.py:125-133` (2048/50.0/4 + 512/512/1024 fallbacks, type flags)
- `core/renderer/shadows/renderer.py:23-30,46-70` (constructor, clamp, `_cascade_resolutions`)
- `core/renderer/shadows/csm.py:54-75` (split computation, `lam = 0.9`)
- `core/renderer/shadows/uniforms.py:29-47,213-214,294` (settings update, biases)
- `core/renderer/shadows/area.py:71-73`, `core/components/lighting/lightp/light.py:90`
