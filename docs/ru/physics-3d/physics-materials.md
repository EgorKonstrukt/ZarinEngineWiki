# Физические материалы

Реакция поверхности на коллайдер: трение держит, упругость отбивает. Ассет или инлайн — назначенный ассет побеждает.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `dynamic_friction` | `0.6` | `≥0` |
| `static_friction` | `0.6` | `≥0` |
| `bounciness` | `0.0` | `≥0` |
| `friction_combine` | `average` | `average` / `minimum` / `maximum` / `multiply` |
| `bounce_combine` | `average` | Тот же набор |
| Файл | `.zphysmat` (JSON) | `save/load/load_cached` |

## Использование в редакторе

Создать `.zphysmat`, сослаться через `Physic Material` любого коллайдера. Один лёд на весь каток вместо десяти настроек.

## Использование в коде

```python
entity = scene.create_entity("Ice")
from core.components.physics.box_collider import BoxCollider
from core.assets.physics_material import PhysicsMaterial

mat = PhysicsMaterial("Ice")
mat.dynamic_friction = 0.05
mat.static_friction = 0.05
mat.save("assets/ice.zphysmat")

box = BoxCollider()
box.physic_material = "assets/ice.zphysmat"  # ассет побеждает инлайн
box.material_friction = 0.6
entity.add_component(box)
```

Правило: `resolve_physics_material` грузит `physic_material` через `load_cached`; при пустом/битом пути — `material_friction`/`material_bounciness` с combine `average`. Комбинации: по имени, по умолчанию `average = (a+b)/2`.

## Заметки о fixed update

- Материалы резолвятся при создании тела; смена ассета подхватывается 60-шаговой проверкой форм. Пошаговой стоимости нет.

## Связанные

Все коллайдеры, слои (реакция vs фильтрация), солвер и потоки.

## Проверено по

- `core/assets/physics_material.py`
- `core/physics/shapep/materials.py`
