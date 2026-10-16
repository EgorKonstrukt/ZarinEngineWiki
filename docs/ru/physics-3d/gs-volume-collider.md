# GS Volume Collider

Объёмы коллизий из Gaussian-splat захватов — игроки ходят по фотограмметрическим сценам без меш-геометрии.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `ply_path` | `""` | `*.ply *.sog *.ssog` |
| `voxel_size` | `0.25` | `0.02–4.0` |
| `opacity_threshold` | `0.05` | `0–1` |
| `dilation` | `1` | `0–3` |
| `max_boxes` | `2048` | `64–16384` |
| `center` | `(0, 0, 0)` | Добавляется к каждому боксу |
| `layer` / `mask` | `0` / `0xFFFF` | Фильтрация |
| `is_trigger` | `False` | Только пересечение |
| `physic_material` | `""` | `.zphysmat` |
| `material_friction` / `material_bounciness` | `0.6` / `0.0` | Запасные |

## Использование в редакторе

`Splat Path`, `Voxel Size` / `Opacity Cutoff` / `Dilation` / `Max Boxes`. Гизмо рисует до 4000 боксов.

## Использование в коде

```python
entity = scene.create_entity("SplatGround")
from core.components.physics.gsvol.collider import GSVolumeCollider

gs = GSVolumeCollider()
gs.ply_path = "assets/capture.ply"
gs.voxel_size = 0.25
gs.max_boxes = 2048
entity.add_component(gs)  # статика: без Rigidbody

def on_collision_enter(self, other_id, force=0.0):
    pass
```

`collider_shapes(transform)` раскрывает объём в список `box`-форм (размер `≥1e-4`, центр с учётом `center` и скейла); пустой объём даёт ноль форм. Без cython-хелперов occupancy/merge `volume_boxes()` пуст.

## Заметки о fixed update

- Формы входят в солвер как обычные `box` через `find_shapes_info` → compound-путь; `dt` — `Engine._fixed_dt` (`0.02`).

## Связанные

Box, Mesh, слои, материалы.

## Проверено по

- `core/components/physics/gsvol/collider.py`
- `core/physics/shapep/shapes.py`
