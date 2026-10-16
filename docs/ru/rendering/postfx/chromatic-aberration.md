# Хроматическая аберрация

> Радиальный/осевой сдвиг цветовых каналов. Каналы R/B сэмплируются со сдвинутых по радиусу UV, масштаб — intensity. Класс `ChromaticAberration` в `core/components/rendering/postfx/chromatic_aberration.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 5.0 | `0.5` |
| Radial | `_radial` | BOOL | — | `True` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `ChromaticAberration`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.chromatic_aberration import ChromaticAberration
fx = ChromaticAberration()
entity.add_component(fx)
fx._intensity = 0.8
fx._radial = True
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/chromatic_aberration.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform int u_radial;
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/chromatic_aberration.py` — класс `ChromaticAberration`, `_inspector_fields`, дефолты `__init__`, `render_type = "screen" (GraphicsEffect base)`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
