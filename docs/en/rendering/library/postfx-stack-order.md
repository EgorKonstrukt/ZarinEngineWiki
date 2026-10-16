# PostFX Stack Order

> The exact frame order — snapshot → shadow pass → opaque/transparent forward phases → additive effects → screen-effect chain → tonemap hook — how to order effects, and how the tonemapping hook works.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

One frame in `SceneRendererMixin.render_scene` (`core/renderer/scenev/mixin.py:24`) runs in this order:

1. **Snapshot** — `_collect_snapshot(scene, ...)` (`mixin.py:54`) gathers lights, sky, renderable entries, sprites, videos, SVGs, particles into a `_RenderSnapshot`.
2. **Shadow pass** — `_scenev_render_shadow_pass(...)` (`mixin.py:144`) renders CSM/point/spot/area shadow maps.
3. **Skybox + forward phases** — sky render (`mixin.py:158`), then `_scenev_partition_and_sort` splits opaque vs transparent (`mixin.py:204`) and `_scenev_render_forward_phases` draws opaque first, transparent second (`core/renderer/scenev/forward.py:19-34`).
4. **Additive effects** — every `GraphicsEffect` with `render_type != "screen"` renders directly into the scene FBO (`output.py:269-281`).
5. **Screen-effect chain** — `render_type == "screen"` effects run ping-pong between `_se_fbo_a` / `_se_fbo_b` in registry order (`output.py:285-324`).
6. **Tonemap hook** — `_present_composite` (`post_pass.py:28-44`) presents the composite texture.

## How to order effects

- Execution order inside each group (additive, screen) is `GraphicsEffect._registry` order — i.e. component creation/enable order (`graphics_effect.py:16,39-54`). There is no numeric priority field; reorder by re-adding components or toggling `enabled`/`entity.active`.
- `render_type` is a class attribute (`graphics_effect.py:18`): most postfx (bloom excluded) use `"screen"`; `Bloom` and lens-flare style effects use `"additive"` and run before the screen chain.
- Skipped effects cost nothing: `should_skip()` (`graphics_effect.py:28-37`) drops disabled/inactive entities and any effect whose `_intensity_prop` is `<= 0.001` (plus `_skip_rate` frame skipping).

## Tonemapping hook

`PostPassMixin._has_tonemap_effect` (`post_pass.py:18-25`):

```python
def _has_tonemap_effect(self):
    if not GraphicsEffect._registry:
        return False
    from core.components.rendering.postfx.color_grading import ColorGrading
    for e in GraphicsEffect._registry:
        if isinstance(e, ColorGrading) and e.enabled and e.entity and e.entity.active:
            return True
    return False
```

`_present_composite` (`post_pass.py:28-44`): if a live `ColorGrading` effect exists (or the tonemap program is missing), the plain copy program is used — tonemapping is then owned by `ColorGrading` in the screen chain. Otherwise the built-in tonemap program runs with `u_exposure` from `self._exposure`.

## Properties

| name | type | default | description |
|---|---|---|---|
| `GraphicsEffect.render_type` | `"additive"` / `"screen"` | `"additive"` | Which group the effect runs in (`graphics_effect.py:18`). |
| `GraphicsEffect._intensity_prop` | string | `""` | Auto-skip knob; effect skipped when `<= 0.001` (`graphics_effect.py:32-36`). |
| `GraphicsEffect._skip_rate` | int | `0` | Render every N+1-th frame when `> 0` (`graphics_effect.py:29-31`). |
| `renderer._exposure` | float | `0.0` | Exposure fed to `u_exposure` only on the built-in tonemap path (`renderer.py:172`, `post_pass.py:41`). |

## Usage in Code

```python
fx.enabled = True           # include in the chain
fx.render_type              # "screen" -> ping-pong chain, "additive" -> scene FBO
GraphicsEffect._registry    # order == execution order within each group
```

## Related pages

- [PostFX Stack](../postfx.md), [Color Grading](../postfx/color-grading.md), [Pipeline](../pipeline.md)

## Verified against

- `core/renderer/scenev/mixin.py:24-210` (`render_scene`: snapshot `:54`, shadow pass `:144`, partition `:204`, forward `:209`)
- `core/renderer/scenev/forward.py:19-34` (opaque then transparent phases)
- `core/renderer/scenev/output.py:207-330` (`_scenev_render_effects`: additive `:269-281`, screen ping-pong `:285-324`, `_present_composite :326`)
- `core/renderer/post_pass.py:18-44` (`_has_tonemap_effect`, `_present_composite`)
- `core/components/rendering/postfx/graphics_effect.py:15-37` (`_registry`, `render_type`, `should_skip`)
