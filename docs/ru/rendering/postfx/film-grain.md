# Зерно плёнки

> Анимированное процедурное зерно. Хэш-шум масштаба size, модуляция по яркости (luminance influence), анимация по времени кадра. Класс `FilmGrain` в `core/components/rendering/postfx/film_grain.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 1.0 | `0.1` |
| Size | `_size` | FLOAT | 0.1 – 10.0 | `1.0` |
| Luminance Influence | `_luminance_influence` | FLOAT | 0.0 – 1.0 | `0.5` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `FilmGrain`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.film_grain import FilmGrain
fx = FilmGrain()
entity.add_component(fx)
fx._intensity = 0.15
fx._size = 1.5
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/film_grain.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform float u_size;
uniform float u_luminance_influence;
uniform float u_time;
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/film_grain.py` — класс `FilmGrain`, `_inspector_fields`, дефолты `__init__`, `GraphicsEffect base, time-driven (u_time)`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
