# Детект краёв

> Наложение краёв по яркостному Собелю 3×3. Магнитуда (веса ~0.2126/0.7152/0.0722) масштабируется intensity и подмешивается к edge color. ЗАМЕЧАНИЕ: тогл `_outline_only` сериализуется, но в текущем EDGE_FRAG ветки outline-only нет — всегда смешивание поверх сцены. Класс `EdgeDetection` в `core/components/rendering/postfx/edge_detection.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 10.0 | `2.0` |
| Edge Color | `_edge_color` | COLOR | — | `(0.0, 0.0, 0.0)` |
| Outline Only | `_outline_only` | BOOL | — | `False (no shader branch; stored only)` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `EdgeDetection`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.edge_detection import EdgeDetection
fx = EdgeDetection()
entity.add_component(fx)
fx._intensity = 2.0
fx._edge_color = (0.0, 0.0, 0.0)
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/edge_detection.py (EDGE_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform vec3 u_edge_color;
uniform vec2 u_pixel_size;
// edge = clamp(length(vec2(gx, gy)) * u_intensity, 0, 1); bg = mix(c, u_edge_color, edge);
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/edge_detection.py` — класс `EdgeDetection`, `_inspector_fields`, дефолты `__init__`, `render_type = "screen", _intensity_prop = "_intensity" (auto-skipped at ≤ 0.001)`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
