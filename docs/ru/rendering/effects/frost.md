# Иней

> Направленное обледенение. Градиент замерзания вдоль direction смешивает цвет льда по coverage × amount; crack scale задаёт частоту трещин; sparkle добавляет блики; rim_power формирует френелевскую кромку. Опциональный флаг animate. Класс `FrostEffect` в `core/components/rendering/effects/frost_effect.py`. ObjectFx pass, same collection/bind path as Dissolve.

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `1.0` |
| Ice Color | `color` | COLOR | — | `[0.8, 0.92, 1.0]` |
| Coverage | `coverage` | SLIDER | 0.0 – 1.0 | `0.7` |
| Crack Scale | `crack_scale` | FLOAT | 0.1 – 20.0 | `3.0` |
| Sparkle | `sparkle` | SLIDER | 0.0 – 2.0 | `0.6` |
| Rim Power | `rim_power` | FLOAT | 0.5 – 8.0 | `3.0` |
| Freeze Direction | `direction` | VEC3 | -1.0 – 1.0 | `(0.0, 1.0, 0.0)` |
| Animate | `animate` | BOOL | — | `False` |

## Использование

Редактор: выбрать меш-сущность, Add Component > `FrostEffect`, включить `enabled`, анимировать amount-поля из скрипта.

Код:

```python
from core.components.rendering.effects.frost_effect import FrostEffect
fx = FrostEffect()
entity.add_component(fx)
fx.amount = 0.8
fx.coverage = 0.9
fx.enabled = True
# каждый кадр: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Шейдер

Шейдер: `core/shaders/internal/ObjectFx.shader (u_frost_* branch)`. Привязанные uniform-ы: `u_frost_amount`, `u_frost_coverage`, `u_frost_crack`, `u_frost_sparkle`, `u_frost_rim`, `u_frost_animate`, `u_time`.

```glsl
// _apply uniforms (exact): u_frost_amount, u_frost_coverage, u_frost_crack, u_frost_sparkle, u_frost_rim, u_frost_animate
```

## Связанное

См. [Эффекты объектов](../effects.md), проход ObjectFx (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Проверено по коду

- `core/components/rendering/effects/frost_effect.py` — класс `FrostEffect`, `_inspector_fields`, дефолты `__init__`, uniform-ы `_apply`.
- `core/components/rendering/effects/object_effect.py` — базовый `ObjectEffect` (`bind` ставит `u_time`, гварды `_set`/`_set_vec`, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
