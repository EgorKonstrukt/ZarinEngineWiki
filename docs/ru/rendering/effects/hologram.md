# Голограмма

> Сканлайн-голография. База тонируется color × amount; горизонтальные полосы (density/speed) + flicker модулируют альфу; rim_power добавляет френелевский край. Класс `HologramEffect` в `core/components/rendering/effects/hologram_effect.py`. ObjectFx pass, additive-ish scanline/fresnel composite.

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `1.0` |
| Color | `color` | COLOR | — | `[0.2, 0.9, 1.0]` |
| Scan Density | `scan_density` | FLOAT | 1.0 – 200.0 | `40.0` |
| Scan Speed | `scan_speed` | FLOAT | 0.0 – 20.0 | `3.0` |
| Flicker | `flicker` | SLIDER | 0.0 – 1.0 | `0.15` |
| Rim Power | `rim_power` | FLOAT | 0.5 – 8.0 | `2.0` |

## Использование

Редактор: выбрать меш-сущность, Add Component > `HologramEffect`, включить `enabled`, анимировать amount-поля из скрипта.

Код:

```python
from core.components.rendering.effects.hologram_effect import HologramEffect
fx = HologramEffect()
entity.add_component(fx)
fx.amount = 1.0
fx.scan_density = 60.0
fx.enabled = True
# каждый кадр: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Шейдер

Шейдер: `core/shaders/internal/ObjectFx.shader (u_holo_* branch)`. Привязанные uniform-ы: `u_holo_amount`, `u_holo_scan`, `u_holo_speed`, `u_holo_flicker`, `u_holo_rim`, `(+ holo color)`, `u_time`.

```glsl
// _apply uniforms (exact): u_holo_amount, u_holo_scan, u_holo_speed, u_holo_flicker, u_holo_rim
```

## Связанное

См. [Эффекты объектов](../effects.md), проход ObjectFx (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Проверено по коду

- `core/components/rendering/effects/hologram_effect.py` — класс `HologramEffect`, `_inspector_fields`, дефолты `__init__`, uniform-ы `_apply`.
- `core/components/rendering/effects/object_effect.py` — базовый `ObjectEffect` (`bind` ставит `u_time`, гварды `_set`/`_set_vec`, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
