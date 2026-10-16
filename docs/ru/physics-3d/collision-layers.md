# Слои коллизий

Кто с кем сталкивается, независимо от реакции поверхности. Каждый коллайдер несёт `layer` (`0`) и `mask` (`0xFFFF`); контакт требует взаимного согласия.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `MAX_LAYERS` | `16` | Слои `0–15` |
| `layer` | `0` | Индекс на коллайдере |
| `mask` | `0xFFFF` | Битмаска |
| Имена | `Default, TransparentFX, Ignore Raycast, Water, UI, Player, Enemy, Projectile, Trigger, Ground, Layer10–15` | `DEFAULT_LAYER_NAMES` |
| Матрица | Все-со-всеми кроме исключений | `build_default_collision_matrix()`: слой `2` ни с кем; слой `4` только сам с собой |

## Использование в редакторе

`Layer` + `Collision Mask` на каждом коллайдере. Типовой план: `0` мир, `5` игрок, `6` враги (выкл. враг-vs-враг), `7` снаряды (мир + персонажи), `8` подборы-триггеры (только игрок).

## Использование в коде

```python
entity = scene.create_entity("Pickup")
from core.components.physics.sphere_collider import SphereCollider

s = SphereCollider()
s.radius = 1.0
s.is_trigger = True
s.layer = 8
s.mask = 1 << 5  # только слой игрока
entity.add_component(s)

from core.physics.collision_layers import build_default_collision_matrix, get_layer_mask
matrix = build_default_collision_matrix()
mask = get_layer_mask([], matrix, 0)

def on_collision_enter(self, other_id, force=0.0):
    pass  # триггеры идут через тот же масочный тест, без отклика
```

Хелперы: `layer_mask_from_names`, `layer_names_from_mask`.

## Заметки о fixed update

- `layer`/`mask` уходят в каждый `create_rigid_body` / compound / soft-вызов и проверяются солвером каждый шаг.
- Режим `per_layer_process` изолирует слои в процессы по требованию (`plugin._get_layer_process`).

## Связанные

Материалы, солвер и потоки, все коллайдеры, Joints.

## Проверено по

- `core/physics/collision_layers.py`
- `core/physics/physics_solver.py`
- `plugins/physics_plug/plugin.py`
