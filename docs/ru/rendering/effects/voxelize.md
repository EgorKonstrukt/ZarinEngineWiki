# Вокселизация

> Развал меша в кубы. CPU-вокселайзер (voxel_cpu.compute_voxel_instances) бинит меш в сетку voxel_size (world_grid — мировой vs локальный снап, jitter — сдвиг); кубы красятся color/emission/rim; amount — кроссфейд; cell — ячейка шейдинга; опционально animate/ping_pong. Класс `VoxelizeEffect` в `core/components/rendering/effects/voxelize_effect.py`. ObjectFx pass + instanced cubes path: _render_voxel_instances() in object_fx_pass.py draws voxel cubes when enabled and amount > 0; show_base_mesh toggles the original mesh draw.

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `0.5` |
| Show Base Mesh | `show_base_mesh` | BOOL | — | `True` |
| World Grid | `world_grid` | BOOL | — | `False` |
| Voxel Size | `voxel_size` | FLOAT | — | `0.3` |
| Voxel Scale XYZ | `scale` | VEC3 | — | `(1.0, 1.0, 1.0)` |
| Jitter | `jitter` | FLOAT | — | `0.0` |
| Glow | `emission` | FLOAT | — | `2.5` |
| Voxel Color | `color` | COLOR | — | `[0.4, 1.0, 0.6]` |
| Rim Power | `rim` | FLOAT | — | `2.5` |
| Cell Size | `cell` | FLOAT | — | `0.3` |
| Double Sided | `double_sided` | BOOL | — | `True` |
| Animate | `animate` | BOOL | — | `False` |
| Anim Speed | `speed_anim` | FLOAT | — | `0.5` |
| Ping Pong | `ping_pong` | BOOL | — | `False` |

## Использование

Редактор: выбрать меш-сущность, Add Component > `VoxelizeEffect`, включить `enabled`, анимировать amount-поля из скрипта.

Код:

```python
from core.components.rendering.effects.voxelize_effect import VoxelizeEffect
fx = VoxelizeEffect()
entity.add_component(fx)
fx.amount = 0.7
fx.voxel_size = 0.25
fx.show_base_mesh = False
fx.enabled = True
# каждый кадр: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Шейдер

Шейдер: `core/components/rendering/effects/voxel_cpu.py + core/shaders/legacy/voxelize.comp + ObjectFx.shader`. Привязанные uniform-ы: `u_vox_amount (+ voxel instance buffers)`, `u_time`.

```glsl
// object_fx_pass.py: show_base = (vox is None) or (not vox.enabled) or vox.show_base_mesh
// if vox.enabled and vox.amount > 0: _render_voxel_instances(entry, vox, ...)
```

## Связанное

См. [Эффекты объектов](../effects.md), проход ObjectFx (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Проверено по коду

- `core/components/rendering/effects/voxelize_effect.py` — класс `VoxelizeEffect`, `_inspector_fields`, дефолты `__init__`, uniform-ы `_apply`.
- `core/components/rendering/effects/object_effect.py` — базовый `ObjectEffect` (`bind` ставит `u_time`, гварды `_set`/`_set_vec`, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
