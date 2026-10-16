# Блики линзы

> Гоуст-блики и анаморфные полосы. Яркость выше порога (с мягким коленом) выделяется, размывается и композитится как масштабированные гоусты, glow и горизонтальная полоса с хроматическим сдвигом. Класс `LensFlares` в `core/components/rendering/postfx/lensp/component.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 5.0 | `1.0` |
| Scale | `_scale` | FLOAT | 0.0 – 5.0 | `1.0` |
| Glow | `_glow` | FLOAT | 0.0 – 5.0 | `1.0` |
| Bright Threshold | `_threshold` | FLOAT | 0.0 – 2.0 | `1.0` |
| Soft Knee | `_soft_threshold` | FLOAT | 0.0 – 1.0 | `0.5` |
| Ghost Intensity | `_ghost_intensity` | FLOAT | 0.0 – 5.0 | `1.0` |
| Anamorphic Streak | `_anamorphic` | FLOAT | 0.0 – 5.0 | `0.6` |
| Chromatic | `_chromatic` | FLOAT | 0.0 – 3.0 | `0.8` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `LensFlares`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.lensp.component import LensFlares
fx = LensFlares()
entity.add_component(fx)
fx._threshold = 1.2
fx._anamorphic = 1.0
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/lensp/shaders.py (FLARE_* / EXTRACT_* )`.

```glsl
// flare uniforms (exact names):
// uniform sampler2D u_scene_color; uniform sampler2D u_bright_tex; uniform sampler2D u_glow_tex;
// uniform float u_intensity; uniform float u_scale; uniform float u_glow;
// uniform float u_threshold; uniform float u_soft_threshold;
// uniform float u_ghost_intensity; uniform float u_anamorphic; uniform float u_chromatic;
```


## Производительность

Bright-extract + downsample chain; cost scales with internal blur resolution, not with threshold.

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/lensp/component.py` — класс `LensFlares`, `_inspector_fields`, дефолты `__init__`, `Bright-extract → downsample → flare composite (ghosts + anamorphic streak)`.

- `core/components/rendering/postfx/lensp/shaders.py`
- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
