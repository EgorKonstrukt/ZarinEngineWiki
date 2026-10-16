# Дизеринг

> Упорядоченный дизеринг + квантование глубины цвета. Bit depth задаёт уровни постеризации; опционально grayscale; intensity подмешивает эффект. Класс `Dithering` в `core/components/rendering/postfx/dithering.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 2.0 | `1.0` |
| Bit Depth | `_bit_depth` | FLOAT | 1.0 – 8.0 | `6.0` |
| Grayscale | `_grayscale` | BOOL | — | `False` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Dithering`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.dithering import Dithering
fx = Dithering()
entity.add_component(fx)
fx._bit_depth = 4.0
fx._intensity = 1.0
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/dithering.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform float u_bit_depth;
uniform float u_grayscale;
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/dithering.py` — класс `Dithering`, `_inspector_fields`, дефолты `__init__`, `GraphicsEffect base, fullscreen pass`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
