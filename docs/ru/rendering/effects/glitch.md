# Глитч (объект)

> Побъектный блочный глитч. Строки UV сдвигаются полосами block_size со скоростью speed, подмес — amount; intensity масштабирует сдвиг блоков, rgb_shift расслаивает каналы. Флаг animate по умолчанию True. Класс `GlitchEffect` в `core/components/rendering/effects/glitch_effect.py`. ObjectFx pass (per-object UV/block shift, unlike fullscreen PostFX Glitch).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `0.3` |
| Block Shift | `intensity` | FLOAT | 0.0 – 2.0 | `0.3` |
| Speed | `speed` | FLOAT | 0.0 – 20.0 | `6.0` |
| Block Size | `block_size` | FLOAT | 0.01 – 2.0 | `0.2` |
| RGB Shift | `rgb_shift` | FLOAT | 0.0 – 3.0 | `1.0` |
| Animate | `animate` | BOOL | — | `True` |

## Использование

Редактор: выбрать меш-сущность, Add Component > `GlitchEffect`, включить `enabled`, анимировать amount-поля из скрипта.

Код:

```python
from core.components.rendering.effects.glitch_effect import GlitchEffect
fx = GlitchEffect()
entity.add_component(fx)
fx.amount = 0.6
fx.intensity = 0.8
fx.animate = True
fx.enabled = True
# каждый кадр: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Шейдер

Шейдер: `core/shaders/internal/ObjectFx.shader (u_glitch_* branch)`. Привязанные uniform-ы: `u_glitch_amount`, `u_glitch_intensity`, `u_glitch_speed`, `u_glitch_block`, `u_glitch_rgb`, `u_time`.

```glsl
// _apply uniforms (exact): u_glitch_amount, u_glitch_intensity, u_glitch_speed, u_glitch_block, u_glitch_rgb
```

## Связанное

См. [Эффекты объектов](../effects.md), проход ObjectFx (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Проверено по коду

- `core/components/rendering/effects/glitch_effect.py` — класс `GlitchEffect`, `_inspector_fields`, дефолты `__init__`, uniform-ы `_apply`.
- `core/components/rendering/effects/object_effect.py` — базовый `ObjectEffect` (`bind` ставит `u_time`, гварды `_set`/`_set_vec`, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
