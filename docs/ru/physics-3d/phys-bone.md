# PhysBone

Динамика цепочек костей для волос, хвостов, плащей, антенн — пружинная симуляция вдоль существующей цепочки. CPU-Verlet, не тело солвера.

## Свойства

| Группа | Поля (по умолчанию) |
|---|---|
| Трансформы | `root_entity_id` `""`, `ignore_entity_ids` `[]`, `ignore_other_phys_bones` `False`, `endpoint_position` `(0,0,0)`, `multi_child_type` `ignore` |
| Силы | `integration_type` `simplified`, `pull` `0.2`, `spring` `0.2`, `damping` `0.1`, `gravity` `0.0`, `gravity_falloff` `0.0`, `immobile_type` `all`, `immobile` `0.0` (у каждого `*_curve` `[[0,1],[1,1]]`) |
| Лимиты | `limit_type` `none`, `max_angle` `45.0` + кривая, `hinge_axis` `(1,0,0)` |
| Коллизия | `radius` `0.11` + кривая, `allow_collision` `True`, `collider_entity_ids` `[]` |
| Stretch | `stretch_motion` `0.0`, `max_stretch` `0.0`, `max_squish` `0.0` (+ кривые) |
| Опции | `parameter` `""`, `is_animated` `False`, `reset_when_disabled` `False`, `allow_grab` `True`, `show_gizmo` `True` |

## Использование в редакторе

Добавить `PhysBone` (можно несколько), задать `Root Transform`, pull/spring/damping, сущности `PhysBoneCollider` — в `Colliders`.

## Использование в коде

```python
entity = scene.create_entity("Hair")
from core.components.physics.physbone.component import PhysBone

pb = PhysBone()
pb.root_entity_id = root_id
pb.pull = 0.2
pb.spring = 0.2
pb.gravity = 0.2
pb.allow_collision = True
pb.collider_entity_ids = [collider_entity_id]
entity.add_component(pb)
```

Цепочка пересобирается в `on_start` / `on_enable`; `on_disable`/`on_destroy` опционально `reset_to_rest()`.

## Заметки о fixed update

- Не шагается `solver.step_simulation`; интеграция покадрово на стороне компонента. Привязки к `Engine._fixed_dt` в проверенном пути нет — коллайдеры сэмплируются через `world_center`/`world_capsule`/`world_plane`.

## Связанные

PhysBoneCollider, Joints (жёсткая альтернатива), солвер и потоки.

## Проверено по

- `core/components/physics/physbone/component.py`, `physbone/params.py`, `physbone/types.py`
