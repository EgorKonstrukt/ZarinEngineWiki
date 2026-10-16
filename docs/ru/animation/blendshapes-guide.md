# Гайд по блендшейпам

Морфинг меша (мимика, мышцы). Компонент `BlendShapes` хранит `shapes: [{name, weight}]`; рендер читает `weights_vector()`, выровненный по `blendshape_names` меша.

## Свойства

| Пункт | Значение |
|---|---|
| `shapes` | `[]` по умолчанию; записи `{"name": str, "weight": float}` |
| Клэмп веса | `_clamp_weight`: не-число/NaN/±inf → `0.0` |
| `get_weight(name_or_index)` / `set_weight(name_or_index, value)` | По имени или индексу; `set_weight` дописывает неизвестные имена, бампит `_weights_version`, возвращает, было ли изменение |
| `reset_weights()` | Обнуляет всё, возвращает, было ли изменение |
| `sync_with_mesh(mesh)` | Перестраивает `shapes` из `mesh.blendshape_names`, сохраняя известные веса |
| `weights_vector(mesh=None)` | Веса в порядке меша, если меш дан, иначе в порядке `shapes` |
| Импорт | `core/assets/importer`, настройка `{"blendshapes": True}` → `mesh.py` вызывает `_finalize_blendshapes` → `blendshape_names / indices / positions / normals` |

Хелпера `gen_blendshapes` в кодовой базе нет: проверенный путь импорта — ассет-импортёр выше плюс `sync_with_mesh`.

## Использование в коде

```python
from core.components.rendering.deform.blendshapes import BlendShapes

bs = BlendShapes()
bs.set_weight("Smile", 0.8)   # допишет {"name": "Smile", "weight": 0.8}
bs.set_weight(0, 0.5)          # по индексу
print(bs.get_weight("Smile"), bs.weight_count, bs.weight_names)
entity.add_component(bs)

# после назначения меша с морфами выровнять список:
# bs.sync_with_mesh(mesh)
# рендер читает: bs.weights_vector(mesh)

# моргание в скрипте:
def on_update(self, dt):
    bs = self._entity.get_component(BlendShapes)
    w = (bs.get_weight("Blink") + dt * 2.0) % 1.0
    bs.set_weight("Blink", w)
```

Поля `weight_*` также доступны через `get_field_value("weight_Smile")` / `set_field_value`, сериализация — списком `shapes`.

## Связанные

Animation Overview, Import Settings, Models (Assimp).

## Проверено по

- `core/components/rendering/deform/blendshapes.py:23-129` (`shapes`, клэмп, get/set/sync/vector)
- `core/assets/importer/settings.py:76` (`"blendshapes": True`)
- `core/assets/importer/mesh.py:143-199` (`want_blend` → `_finalize_blendshapes`)
- `core/assets/importer/anim.py:319-364` (`blendshape_names/indices/positions/normals`)
