# Mesh-коллайдер

Коллизии из геометрии меша там, где примитивы не ложатся: статуи, корпуса техники, вогнутые декорации.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `mesh_path` | `""` | `*.obj *.stl *.gltf *.glb` |
| `collision_mode` | `auto` | `auto` / `mesh` / `convex_hull` / `box` / `sphere` |
| `max_vertices` | `2000` | `0–100000`, бюджет hull |
| `center` | `(0, 0, 0)` | Смещение |
| `layer` / `mask` | `0` / `0xFFFF` | Фильтрация |
| `is_trigger` | `False` | Только пересечение |
| `physic_material` | `""` | `.zphysmat` |
| `material_friction` / `material_bounciness` | `0.6` / `0.0` | Запасные |

## Использование в редакторе

Назначить `Mesh`, оставить `auto` (статика → точный `mesh`, динамика → `convex_hull`). Hull держать в `max_vertices`.

## Использование в коде

```python
entity = scene.create_entity("Statue")
from core.components.physics.meshcol.collider import MeshCollider

mc = MeshCollider()
mc.mesh_path = "assets/statue.obj"
mc.max_vertices = 2000
entity.add_component(mc)  # без Rigidbody = статика

def on_collision_enter(self, other_id, force=0.0):
    pass
```

Правило режимов: `AUTO` → `MESH` для статики, `CONVEX_HULL` для динамики; явный `MESH` на динамике форсируется в `CONVEX_HULL`. Меш на динамическом теле даунгрейдится до бокса (`downgrade_mesh_to_box` с варнингом); остальные непримитивы — отдельные статичные тела.

## Заметки о fixed update

- Вызов: `create_rigid_body(shape_type="mesh", ...)`; `dt` — `Engine._fixed_dt` (`0.02`).

## Связанные

Rigidbody, Box (цель даунгрейда), Terrain, материалы, слои.

## Проверено по

- `core/components/physics/meshcol/collider.py`, `meshcol/mode.py`
- `core/physics/shapep/shapes.py`
