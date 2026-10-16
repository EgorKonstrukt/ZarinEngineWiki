# PhysBoneCollider

Отражатели, держащие цепочки PhysBone вне тел: волосы — из головы, плащ — из ног. Только CPU, как сам PhysBone.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `shape_type` | `sphere` | `sphere` / `capsule` / `plane` |
| `radius` | `0.05` | `0.001–10`; мир = `radius * max(scale)`, мин. `0.001` |
| `height` | `0.2` | Полная высота капсулы вдоль `direction` |
| `center` | `(0, 0, 0)` | Поворачивается мировым кватернионом, прибавляется к позиции |
| `direction` | `y` | Ось капсулы `x` / `y` / `z` |
| `plane_normal` | `(0, 1, 0)` | Поворачивается в мир для плоскостей |
| `is_inside` | `False` | `True` = удерживать внутри вместо выталкивания |
| `show_gizmo` | `True` | Гизмо + линии (только плоскости) |

## Использование в редакторе

Добавить `PhysBoneCollider`, выбрать форму и размер, сослаться на сущность из `PhysBone.collider_entity_ids`. `is_inside` — зоны удержания.

## Использование в коде

```python
entity = scene.create_entity("HeadBlocker")
from core.components.physics.pbcollider.collider import PhysBoneCollider
from core.maths.math3d import Vec3

c = PhysBoneCollider()
c.radius = 0.15
c.center = Vec3(0.0, 0.1, 0.0)
entity.add_component(c)

# Выталкивание из сферы с учётом радиуса частицы:
# pos = c.collide(pos, particle_radius)
```

`collide(pos, particle_radius)`: сфера/капсула проецируют на поверхность `radius + pr` (или клампят внутрь при `is_inside`); плоскости толкают вдоль `±normal` на глубину проникновения.

## Заметки о fixed update

- Нет тела солвера и `dt`: чистая позиционная проекция в проходе PhysBone каждый кадр.

## Связанные

PhysBone, Sphere/Capsule (жёсткие аналоги).

## Проверено по

- `core/components/physics/pbcollider/collider.py`, `pbcollider/base.py`
