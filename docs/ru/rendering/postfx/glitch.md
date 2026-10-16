# Глитч (пост)

> Цифровой глитч: блочное смещение сканлайнов + расслоение каналов, темп — speed и частота полос, сила — intensity. Класс `Glitch` в `core/components/rendering/postfx/glitch.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 3.0 | `0.5` |
| Speed | `_speed` | FLOAT | 0.0 – 20.0 | `3.0` |
| Frequency | `_frequency` | FLOAT | 1.0 – 100.0 | `20.0` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Glitch`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.glitch import Glitch
fx = Glitch()
entity.add_component(fx)
fx._intensity = 1.0
fx._speed = 5.0
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/glitch.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform float u_speed;
uniform float u_frequency;
uniform float u_time;
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/glitch.py` — класс `Glitch`, `_inspector_fields`, дефолты `__init__`, `Time-driven fullscreen pass (u_time, u_resolution)`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
