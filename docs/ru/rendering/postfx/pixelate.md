# Пикселизация

> Ретро-мозаика. UV квантуются блоками pixel_size относительно разрешения рендера. Класс `Pixelate` в `core/components/rendering/postfx/pixelate.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Pixel Size | `_pixel_size` | FLOAT | 1.0 – 256.0 | `8.0` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Pixelate`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.pixelate import Pixelate
fx = Pixelate()
entity.add_component(fx)
fx._pixel_size = 12.0
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/pixelate.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_resolution;
uniform float u_pixel_size;
```


## Производительность

Single pass, resolution-independent cost.

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/pixelate.py` — класс `Pixelate`, `_inspector_fields`, дефолты `__init__`, `Fullscreen nearest-block pass (u_resolution)`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
