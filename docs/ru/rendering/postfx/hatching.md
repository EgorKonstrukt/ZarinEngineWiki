# Штриховка

> Стиль чернила-и-бумага. Собель по глубине + восстановленной нормали ищет края; яркостные полосы выбирают одну из трёх процедурных штрих-текстур; Perlin-джиттер UV + дисторсия по нормали дают дрожание руки; итог смешивается от paper/ink к фото по intensity. Класс `Hatching` в `core/components/rendering/postfx/hatching.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 1.0 | `1.0` |
| Edge Color | `_edge_color` | COLOR | — | `(0.0, 0.0, 0.0)` |
| Edge Threshold | `_edge_threshold` | FLOAT | 0.001 – 0.5 | `0.05` |
| Hatch Scale | `_hatch_scale` | FLOAT | 2.0 – 60.0 | `20.0` |
| Noise Frequency | `_noise_frequency` | FLOAT | 0.1 – 20.0 | `10.0` |
| Noise Offset | `_noise_offset_intensity` | FLOAT | 0.0 – 0.05 | `0.002` |
| Hatch Distort | `_distort` | FLOAT | 0.0 – 0.3 | `0.04` |
| Normal Strength | `_normal_strength` | FLOAT | 0.1 – 5.0 | `1.0` |
| Ink Color | `_ink_color` | COLOR | — | `(0.08, 0.07, 0.09)` |
| Paper Color | `_paper_color` | COLOR | — | `(0.95, 0.93, 0.86)` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Hatching`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.hatching import Hatching
fx = Hatching()
entity.add_component(fx)
fx._hatch_scale = 24.0
fx._edge_threshold = 0.06
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/hatching.py (HATCHING_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform sampler2D u_depth_tex;
uniform vec2 u_pixel_size;
uniform float u_intensity;
uniform vec3 u_edge_color;
uniform float u_edge_threshold;
```


## Производительность

Depth Sobel (3×3 depth + 3×3 normal) + up to 3 hatch texture fetches; hatch textures generated once (`_build_hatch_textures`).

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/hatching.py` — класс `Hatching`, `_inspector_fields`, дефолты `__init__`, `render_type = "screen", _intensity_prop = "_intensity". Depth-driven: needs u_depth_tex; builds 3 procedural hatch textures at runtime.`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
