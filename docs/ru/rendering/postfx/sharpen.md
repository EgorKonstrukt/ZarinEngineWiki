# Шарпенинг

> Подъём деталей типа unsharp. Высокие частоты (центр − среднее окрестности) добавляются обратно с масштабом intensity. Класс `Sharpen` в `core/components/rendering/postfx/sharpen.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 5.0 | `0.5` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Sharpen`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.sharpen import Sharpen
fx = Sharpen()
entity.add_component(fx)
fx._intensity = 0.8
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/sharpen.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform vec2 u_pixel_size;
```


## Производительность

Fixed 3×3 neighbourhood, single pass.

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/sharpen.py` — класс `Sharpen`, `_inspector_fields`, дефолты `__init__`, `Single 3×3 contrast-adaptive pass (u_pixel_size)`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
