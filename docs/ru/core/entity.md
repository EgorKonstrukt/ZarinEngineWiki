# Сущность Entity

> `Entity` — объект сцены: UUID `id`, `name`, иерархия parent/children, `tags`, `layer`, тогл `active` и типизированное хранилище компонентов.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

`Entity(name="Entity", eid=None, prefab_guid=None)` генерирует UUID, если `eid` не передан. Компоненты живут в `_components` (ключ → инстанс) плюс `_type_map` (класс → список). Одиночные компоненты используют имя класса как ключ; `_allow_multiple`-компоненты получают ключи `ClassName.<8-hex>`. `add_component` сразу вызывает `comp.on_awake()`; `remove_*` вызывают `on_destroy()`. Переключение `active` кидает `on_enable`/`on_disable` на enabled-компонентах и добавляет/убирает их из активных update-сетов сцены. `set_parent(parent, preserve_world=True)` по умолчанию сохраняет мировую матрицу.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| id | str | UUID4 | Стабильная идентичность; ключ словаря сцены; сериализуется |
| name | str | `"Entity"` | Отображаемое имя; `get_entity_by_name` ищет по нему |
| active | bool | `True` | Неактивные сущности вне активных update-сетов; кидаются `on_enable`/`on_disable` |
| parent | Entity\|None | `None` | Связь иерархии; `Scene.get_root_entities()` возвращает сущности без родителя |
| children | list[Entity] | `[]` | Прямые дети; `remove_entity` рекурсирует по ним |
| tags | set[str] | `set()` | Произвольные теги; сериализуются списком |
| layer | int | `0` | Целочисленный слой; сериализуется |
| transform | Component\|None | cached `Transform` | Быстрый путь к `Transform` сущности |
| prefab_guid | str\|None | `None` | Установлен → `is_prefab_instance` равен `True` |
| prefab_source_id | str\|None | `None` | Id исходной сущности внутри префаба |
| embed_resources | bool | `False` | Встроить ресурсы этой сущности при сохранении |
| locked | bool | `False` | Флаг лока редактора (напр. `Shadow System`) |
| system | bool | `False` | Флаг системной сущности (напр. `Shadow System`) |

## Использование в редакторе

Панель Hierarchy: создание, переименование, удаление, дублирование, drag-to-reparent, чекбокс `active`. Locked/system-сущности (напр. `Shadow System`) защищены от случайных правок.

## Использование в коде

```python
from core.components.transform.tr import Transform

player = scene.create_entity("Player")
player.tags.add("player")
player.layer = 5
tr = player.add_component(Transform())

child = scene.create_entity("Gun")
child.set_parent(player)  # preserve_world=True by default

got = player.get_component(Transform)            # first match incl. subclasses
by_name = player.get_component_by_name("Transform")
all_tr = player.get_components(Transform)
player.remove_component(Transform)
scene.remove_entity(player.id)  # recursive over children
```

```python
clone = scene.duplicate_entity(player, "PlayerCopy")  # свежие UUID, перебинд Armature bone ids
print(player.has_component(Transform), player.is_prefab_instance)
scene.get_root_entities()  # сущности без родителя
```

## Сериализация

```json
{ "id": "<uuid>", "name": "Player", "active": true, "tags": ["player"],
  "layer": 5, "parent": null,
  "components": [{ "type": "Transform", "enabled": true, "_key": "Transform", "...": "..." }] }
```

Опциональные ключи появляются только когда заданы: `prefab_guid`, `prefab_source_id`, `embed_resources: true`, `locked: true`, `system: true`.

## Связанные страницы

- [Обзор ECS](./ecs-overview.md)
- [Компоненты и жизненный цикл](./components-lifecycle.md)
- [Трансформ](./transform.md)
- [Реестр и сериализация](./registry-serialization.md)

## Проверено по

- `core/ecs/entity.py` (slots, дефолты, `set_parent`, add/remove/get, `serialize`/`deserialize`)
- `core/ecs/scene.py` (`create_entity`, `add_entity`, `remove_entity`, `duplicate_entity`, `get_root_entities`)
- `core/engine/eng/engine.py` (`_ensure_shadow_system`: `system=True`, `locked=True`)
