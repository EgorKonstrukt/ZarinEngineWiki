# Цветокоррекция

> Экспозиция / баланс белого / контраст / насыщенность с выбираемым тонемаппером (Off, ACES, Reinhard, Neutral). Порядок в шейдере: exposure → white balance → tonemap → contrast → saturation. Класс `ColorGrading` в `core/components/rendering/postfx/color_grading.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Exposure (EV) | `_exposure` | FLOAT | -5.0 – 5.0 | `0.0` |
| Contrast | `_contrast` | FLOAT | 0.0 – 3.0 | `1.0` |
| Saturation | `_saturation` | FLOAT | 0.0 – 3.0 | `1.0` |
| Temperature | `_temperature` | FLOAT | -1.0 – 1.0 | `0.0` |
| Tint | `_tint` | FLOAT | -1.0 – 1.0 | `0.0` |
| Tonemapping | `_tonemap_mode` | ENUM TonemapMode {Off, ACES, Reinhard, Neutral} → int 0–3 | — | `Off (0)` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `ColorGrading`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.color_grading import ColorGrading, TonemapMode
fx = ColorGrading()
entity.add_component(fx)
fx._exposure = 0.5
fx._contrast = 1.1
fx._tonemap_mode = TonemapMode.ACES
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/color_grading.py (CG_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_exposure;
uniform float u_contrast;
uniform float u_saturation;
uniform float u_temperature;
uniform float u_tint;
uniform int u_tonemap_mode;  // 0 Off, 1 ACES, 2 Reinhard, 3 Neutral
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/color_grading.py` — класс `ColorGrading`, `_inspector_fields`, дефолты `__init__`, `render_type = "screen". PostPassMixin._has_tonemap_effect() returns True when an enabled active ColorGrading exists, switching the present path to copy instead of the fallback tonemap program.`.

- `core/renderer/post_pass.py`
- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
