# Порядок стека PostFX

> Точный порядок кадра — snapshot → проход теней → opaque/transparent forward-фазы → аддитивные эффекты → цепочка screen-эффектов → зацеп тонемаппинга — как упорядочить эффекты и как работает зацеп тонемаппинга.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Один кадр `SceneRendererMixin.render_scene` (`core/renderer/scenev/mixin.py:24`) идёт в таком порядке:

1. **Snapshot** — `_collect_snapshot(scene, ...)` (`mixin.py:54`) собирает свет, небо, рисуемые entries, спрайты, видео, SVG, частицы в `_RenderSnapshot`.
2. **Проход теней** — `_scenev_render_shadow_pass(...)` (`mixin.py:144`) рисует CSM/point/spot/area карты теней.
3. **Скайбокс + forward-фазы** — рендер неба (`mixin.py:158`), затем `_scenev_partition_and_sort` делит opaque и transparent (`mixin.py:204`), а `_scenev_render_forward_phases` рисует сначала opaque, потом transparent (`core/renderer/scenev/forward.py:19-34`).
4. **Аддитивные эффекты** — каждый `GraphicsEffect` с `render_type != "screen"` рисует прямо в scene FBO (`output.py:269-281`).
5. **Цепочка screen-эффектов** — эффекты с `render_type == "screen"` идут пинг-понгом между `_se_fbo_a` / `_se_fbo_b` в порядке реестра (`output.py:285-324`).
6. **Зацеп тонемаппинга** — `_present_composite` (`post_pass.py:28-44`) выводит композитную текстуру.

## Как упорядочить эффекты

- Порядок выполнения внутри каждой группы (additive, screen) — это порядок `GraphicsEffect._registry`, т.е. порядок создания/включения компонентов (`graphics_effect.py:16,39-54`). Числового поля приоритета нет; переупорядочивание — пересозданием компонентов или тогглом `enabled`/`entity.active`.
- `render_type` — атрибут класса (`graphics_effect.py:18`): почти весь postfx — `"screen"`; `Bloom` и lens-flare-подобные — `"additive"` и идут до screen-цепочки.
- Пропущенные эффекты ничего не стоят: `should_skip()` (`graphics_effect.py:28-37`) отбрасывает выключенные/неактивные entity и эффекты, чей `_intensity_prop <= 0.001` (плюс пропуск кадров через `_skip_rate`).

## Зацеп тонемаппинга

`PostPassMixin._has_tonemap_effect` (`post_pass.py:18-25`):

```python
def _has_tonemap_effect(self):
    if not GraphicsEffect._registry:
        return False
    from core.components.rendering.postfx.color_grading import ColorGrading
    for e in GraphicsEffect._registry:
        if isinstance(e, ColorGrading) and e.enabled and e.entity and e.entity.active:
            return True
    return False
```

`_present_composite` (`post_pass.py:28-44`): если живой эффект `ColorGrading` есть (или tonemap-программа отсутствует), используется plain copy — тонемаппингом тогда владеет `ColorGrading` в screen-цепочке. Иначе работает встроенная tonemap-программа с `u_exposure` из `self._exposure`.

## Properties

| name | type | default | description |
|---|---|---|---|
| `GraphicsEffect.render_type` | `"additive"` / `"screen"` | `"additive"` | В какой группе идёт эффект (`graphics_effect.py:18`). |
| `GraphicsEffect._intensity_prop` | string | `""` | Ручка авто-пропуска; эффект скипается при `<= 0.001` (`graphics_effect.py:32-36`). |
| `GraphicsEffect._skip_rate` | int | `0` | Рендер каждый N+1-й кадр при `> 0` (`graphics_effect.py:29-31`). |
| `renderer._exposure` | float | `0.0` | Экспозиция в `u_exposure` только на встроенном tonemap-пути (`renderer.py:172`, `post_pass.py:41`). |

## Usage in Code

```python
fx.enabled = True           # включить в цепочку
fx.render_type              # "screen" -> пинг-понг цепочка, "additive" -> scene FBO
GraphicsEffect._registry    # порядок == порядок выполнения внутри группы
```

## Related pages

- [Стек PostFX](../postfx.md), [Цветокоррекция](../postfx/color-grading.md), [Конвейер](../pipeline.md)

## Verified against

- `core/renderer/scenev/mixin.py:24-210` (`render_scene`: snapshot `:54`, проход теней `:144`, partition `:204`, forward `:209`)
- `core/renderer/scenev/forward.py:19-34` (сначала opaque-, затем transparent-фазы)
- `core/renderer/scenev/output.py:207-330` (`_scenev_render_effects`: additive `:269-281`, screen пинг-понг `:285-324`, `_present_composite :326`)
- `core/renderer/post_pass.py:18-44` (`_has_tonemap_effect`, `_present_composite`)
- `core/components/rendering/postfx/graphics_effect.py:15-37` (`_registry`, `render_type`, `should_skip`)
