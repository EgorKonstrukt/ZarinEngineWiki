# Chromatic Aberration

> Radial / axial color-channel shift. Samples R/B at radially offset UVs scaled by intensity. Class `ChromaticAberration` in `core/components/rendering/postfx/chromatic_aberration.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 5.0 | `0.5` |
| Radial | `_radial` | BOOL | — | `True` |

## Usage

Editor: select the PostFX entity, Add Component > `ChromaticAberration`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.chromatic_aberration import ChromaticAberration
fx = ChromaticAberration()
entity.add_component(fx)
fx._intensity = 0.8
fx._radial = True
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/chromatic_aberration.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform int u_radial;
```


## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/chromatic_aberration.py` — class `ChromaticAberration`, `_inspector_fields`, `__init__` defaults, `render_type = "screen" (GraphicsEffect base)`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
