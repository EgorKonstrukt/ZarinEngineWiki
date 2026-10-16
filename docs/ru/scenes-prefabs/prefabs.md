# Префабы и библиотека

`Prefab` пакует поддеревья сущностей в JSON (`.zpep`), инстансит с новыми GUID и ведёт оверрайды инстансов.

## Свойства / API

| Элемент | Источник | Смысл |
|---|---|---|
| `Prefab(name, guid, base_guid, menu_path)` | `core/ecs/prefabp/prefab.py` | `guid`, `base_guid` = вариант, `roots_data` |
| `capture(entities)` | `prefab.py` | `Entity.serialize` + `source_id`, рекурсия детей |
| `save(path)` | `prefab.py` | JSON `{guid, name, roots[, base_guid, menu_path]}` |
| `instantiate(scene, registry, parent)` | `prefab.py` | Дип-копия, `_remap_ids` (uuid4), простановка `_prefab_guid` |
| `instantiate_plain` | `prefab.py` | Инстанс + `Prefab.unpack` (отвязка) |
| `PrefabOverrideType` | `prefab.py` | `modified_property / added_component / removed_component / added_child / removed_child` |
| `compute_overrides / apply_overrides` | `overrides.py` | Дифф инстанса и запись в `.zpep` |
| `PrefabLibrary` | `library.py` | Кэш ассетов |
| Фильтр | `network_spawn.py`, `scriptc/fields.py` | `Prefab (*.zpep)` |

## Использование в редакторе

1. Выберите корень, создайте ассет `.zpep` (`guid`, `name`, `roots`).
2. Инстансы в сценах; правки инстанса — оверрайды, не правки ассета.
3. Apply записывает дифф обратно в `.zpep`.

## Использование в коде

```python
from core.ecs.prefabp.prefab import Prefab

pref = Prefab(name="Enemy")
pref.capture([enemy_entity])
pref.save("assets/prefabs/enemy.zpep")
spawned = pref.instantiate(scene, registry, parent=None)
```

## Связанные

- Сцены, ECS Entity, сетевой спавн

## Проверено по

- `core/ecs/prefabp/prefab.py`, `overrides.py`, `library.py`, `core/prefabs/registry.py`
