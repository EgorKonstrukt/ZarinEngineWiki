# Bloom

> Экранное свечение. Пиксели ярче порога выделяются префильтром, размываются по даунсэмпленной mip-цепочке и добавляются обратно, опционально с dirt-текстурой линзы. Класс `Bloom` в `core/components/rendering/postfx/bloomp/__init__.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 5.0 | `1.0` |
| Threshold | `_threshold` | FLOAT | 0.0 – 5.0 | `1.5` |
| Max Color | `_max_color` | FLOAT | 0.5 – 10.0 | `3.8` |
| Downsample | `_downsample` | INT | 1 – 8 | `2` |
| Diffusion | `_diffusion` | FLOAT | 0.5 – 5.0 | `1.0` |
| Dirt Texture | `_dirt_texture` | RESOURCE_PATH (Images *.png *.jpg *.jpeg *.tga *.bmp) | — | `""` |
| Dirt Intensity | `_dirt_intensity` | FLOAT | 0.0 – 5.0 | `0.0` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Bloom`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.bloomp import Bloom
fx = Bloom()
entity.add_component(fx)
fx._intensity = 1.0
fx._threshold = 1.5
fx._downsample = 2
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/bloomp/threshold.py (PREFILTER_FRAG), bloomp/blur.py (DOWN_FRAG / UP_FRAG), bloomp/composite.py (OUTPUT_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_threshold;
uniform float u_max_color;   // prefilter
// blur: uniform float u_diffusion; uniform sampler2D u_down_tex;
// composite: uniform sampler2D u_dirt_tex; uniform float u_dirt_intensity; uniform int u_has_dirt; uniform float u_intensity;
```


## Производительность

Mip-chain cost scales with Downsample (1–8, default 2) and Diffusion. Skipped automatically when `_intensity <= 0.001` (`GraphicsEffect.should_skip`, `_intensity_prop = "_intensity"`).

## Связанное

См. [Стек PostFX](../postfx.md), [Blur](blur.md), [Цветокоррекция](color-grading.md).

## Проверено по коду

- `core/components/rendering/postfx/bloomp/__init__.py` — класс `Bloom`, `_inspector_fields`, дефолты `__init__`, `render_type = "additive", _intensity_prop = "_intensity"`.

- `core/components/rendering/postfx/bloomp/threshold.py`
- `core/components/rendering/postfx/bloomp/blur.py`
- `core/components/rendering/postfx/bloomp/composite.py`
- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
