# Настройка теней

> Дефолты разрешения/дистанции/каскадов по типам света, константы биаса и заметки по производительности — всё из кода.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

У настроек теней два слоя: компонент сцены `DirectionalShadow` (авторские значения) и фолбэки рендерера (`core/renderer/renderer.py:125-131`) на случай отсутствия компонента. Бэкенд — `ShadowRenderer` (`core/renderer/shadows/renderer.py:23-30`).

## Properties

| setting | авторский дефолт (`DirectionalShadow`) | фолбэк рендерера | код |
|---|---|---|---|
| Разрешение directional | `4096` | `2048` | `directional_shadow.py:45`, `renderer.py:125` |
| Дистанция теней | `50.0` м | `50.0` м | `directional_shadow.py:46`, `renderer.py:126` |
| Каскады | `4` | `4` | `directional_shadow.py:48`, `renderer.py:127` |
| Сплиты каскадов (4) | `[0.05, 0.13, 0.3]` | вычисляемый log/linear-бленд (`lam = 0.9`) | `directional_shadow.py:18`, `csm.py:69-75` |
| Разрешение point | — | `512` | `renderer.py:129` |
| Разрешение area | — | `512` | `renderer.py:130` |
| Разрешение spot | — | `1024` | `renderer.py:131` |
| `u_shadow_bias` | — | `0.0015` | `shadows/uniforms.py:213` |
| `u_shadow_normal_bias` | — | `0.02` | `shadows/uniforms.py:214` |
| `area_shadow_bias` | `0.005` (на `AreaLight`) | `0.005` | `light.py:90`, `shadows/area.py:71-73` |

Разрешения тайлов каскадов для 4-каскадной карты выводятся из базового: `[base, max(512, base//2), max(512, base//2), max(256, base//4)]` (`shadows/uniforms.py:33`), статическая атласная раскладка рендерера — `[2048, 1024, 1024, 512]` (`shadows/renderer.py:70`).

## Гайд по настройке

- **Directional**: слайдер разрешения 256–4096 шаг 256, дистанция 1–500 м, каскады 1–4 (`directional_shadow.py:33-40`). Авторский дефолт — 4096 + 4 каскада; для слабого железа — 2048/2 каскада.
- **Сплиты**: дефолты по числу — `{2: [0.5], 3: [0.2, 0.6], 4: [0.05, 0.13, 0.3]}` (`directional_shadow.py:18`, зеркало в `editor/cascade_split_widget.py:15`). Сдвигайте наружу для дальней земли, внутрь — для деталей вблизи.
- **Point/spot/area**: фиксированы в рендерере как 512/1024/512; вместо глобального понижения выключайте `cast_shadows` на заполняющем свете.
- **Акне vs peter-panning**: поднимайте `u_shadow_bias`/`u_shadow_normal_bias` против акне; `area_shadow_bias` (0–0.1, шаг 0.001) — отдельно на area-свет (`shadows.py:100`).

## Заметки по производительности

Стоимость теней растёт как число каскадов × разрешение тайла плюс один проход ~512² на point-свет и один 1024² на спот. `ShadowRenderer` клампит каскады к 1–4 (`shadows/renderer.py:25`). Очередь обводок ограничена 512 entries (`output.py:397`).

## Usage in Code

```python
from core.components.rendering.environment.directional_shadow import DirectionalShadow
ds = DirectionalShadow()
ds._shadow_resolution = 4096
ds._shadow_distance = 50.0
ds._set_cascade_count(4)
```

## Related pages

- [Тени](../shadows.md), [Направленный свет](../directional-light.md), [Физические единицы света](./light-units.md)

## Verified against

- `core/components/rendering/environment/directional_shadow.py:18-48` (таблица сплитов, дефолты 4096/50.0/4, слайдеры)
- `core/renderer/renderer.py:125-133` (фолбэки 2048/50.0/4 + 512/512/1024, флаги типов)
- `core/renderer/shadows/renderer.py:23-30,46-70` (конструктор, кламп, `_cascade_resolutions`)
- `core/renderer/shadows/csm.py:54-75` (вычисление сплитов, `lam = 0.9`)
- `core/renderer/shadows/uniforms.py:29-47,213-214,294` (обновление настроек, биасы)
- `core/renderer/shadows/area.py:71-73`, `core/components/lighting/lightp/light.py:90`
