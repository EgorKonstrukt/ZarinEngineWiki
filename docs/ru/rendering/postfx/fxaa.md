# FXAA

> Быстрое приближённое сглаживание. Детект края по яркости с субпиксельным блендом, ранний выход по контрасту (reduce / reduce_min) и кламп максимальной длины (span). Класс `FXAA` в `core/components/rendering/postfx/fxaa.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Subpixel | `_subpix` | FLOAT | 0.0 – 1.0 | `1.0` |
| Reduce | `_reduce` | FLOAT | 0.0 – 1.0 | `0.125` |
| Reduce Min | `_reduce_min` | FLOAT | 0.0 – 0.1 | `0.0078125` |
| Span | `_span` | FLOAT | 1.0 – 16.0 | `8.0` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `FXAA`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.fxaa import FXAA
fx = FXAA()
entity.add_component(fx)
fx._subpix = 0.75
fx._span = 8.0
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/fxaa.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_pixel_size;
uniform float u_subpix;
uniform float u_reduce;
uniform float u_reduce_min;
```


## Производительность

Single pass, fixed tap count; Span (1–16) bounds the edge search.

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/fxaa.py` — класс `FXAA`, `_inspector_fields`, дефолты `__init__`, `Single fullscreen luma-edge AA pass`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
