# Сканлайны

> CRT-оверлей: затемнение горизонтальных полос с частотой frequency, бочкообразная кривизна, угловая виньетка и поканальный RGB-сдвиг. Класс `Scanlines` в `core/components/rendering/postfx/scanlines.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 1.0 | `0.3` |
| Frequency | `_frequency` | FLOAT | 1.0 – 500.0 | `200.0` |
| Curvature | `_curvature` | FLOAT | 0.0 – 1.0 | `0.1` |
| Vignette | `_vignette` | FLOAT | 0.0 – 1.0 | `0.3` |
| RGB Offset | `_rgb_offset` | FLOAT | 0.0 – 0.05 | `0.002` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Scanlines`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.scanlines import Scanlines
fx = Scanlines()
entity.add_component(fx)
fx._frequency = 240.0
fx._intensity = 0.4
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/scanlines.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_resolution;
uniform float u_intensity;
uniform float u_frequency;
uniform float u_curvature;
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/scanlines.py` — класс `Scanlines`, `_inspector_fields`, дефолты `__init__`, `Fullscreen CRT overlay (u_resolution)`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
