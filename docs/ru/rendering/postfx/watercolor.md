# Акварель

> Акварельный вид. Среднее окрестности (тапы brush_size) затемняется по краям и смешивается по влажности: wetness — растекание пигмента, intensity — подмес к фото. Класс `Watercolor` в `core/components/rendering/postfx/watercolor.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Brush Size | `_brush_size` | FLOAT | 1.0 – 8.0 | `4.0` |
| Edge Darken | `_edge_darken` | FLOAT | 0.0 – 2.0 | `0.8` |
| Wetness | `_wetness` | FLOAT | 0.0 – 1.0 | `0.5` |
| Intensity | `_intensity` | FLOAT | 0.0 – 1.0 | `1.0` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Watercolor`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.watercolor import Watercolor
fx = Watercolor()
entity.add_component(fx)
fx._brush_size = 5.0
fx._wetness = 0.6
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/watercolor.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_pixel_size;
uniform float u_brush_size;
uniform float u_edge_darken;
uniform float u_wetness;
```


## Производительность

Tap count grows with Brush Size (1–8).

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/watercolor.py` — класс `Watercolor`, `_inspector_fields`, дефолты `__init__`, `Fullscreen painterly pass (u_pixel_size)`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
