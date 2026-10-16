# Emissive-пульс

> Синусоидальный подъём эмиссии. Цвет эмиссии × strength × (0.5 + 0.5·sin(u_time·speed)) добавляется, гейт — amount. Класс `EmissivePulseEffect` в `core/components/rendering/effects/emissive_pulse_effect.py`. ObjectFx pass, same collection/bind path as Dissolve.

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `1.0` |
| Pulse Color | `color` | COLOR | — | `[1.0, 0.4, 0.1]` |
| Speed | `speed` | FLOAT | 0.0 – 20.0 | `3.0` |
| Intensity | `strength` | FLOAT | 0.0 – 10.0 | `2.0` |

## Использование

Редактор: выбрать меш-сущность, Add Component > `EmissivePulseEffect`, включить `enabled`, анимировать amount-поля из скрипта.

Код:

```python
from core.components.rendering.effects.emissive_pulse_effect import EmissivePulseEffect
fx = EmissivePulseEffect()
entity.add_component(fx)
fx.amount = 1.0
fx.strength = 3.0
fx.speed = 4.0
fx.enabled = True
# каждый кадр: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Шейдер

Шейдер: `core/shaders/internal/ObjectFx.shader (u_pulse_* branch)`. Привязанные uniform-ы: `u_pulse_amount`, `u_pulse_speed`, `u_pulse_strength`, `(+ color buffer)`, `u_time`.

```glsl
// _apply uniforms (exact): u_pulse_amount, u_pulse_speed, u_pulse_strength (+ pulse color vec3)
```

## Связанное

См. [Эффекты объектов](../effects.md), проход ObjectFx (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Проверено по коду

- `core/components/rendering/effects/emissive_pulse_effect.py` — класс `EmissivePulseEffect`, `_inspector_fields`, дефолты `__init__`, uniform-ы `_apply`.
- `core/components/rendering/effects/object_effect.py` — базовый `ObjectEffect` (`bind` ставит `u_time`, гварды `_set`/`_set_vec`, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
