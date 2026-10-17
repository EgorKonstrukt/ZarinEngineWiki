# Префабы из кода

Создание, сохранение и спавн префабов из кода, плюс пять типов оверрайдов у инстансов.

## Объект Prefab

| Сигнатура | Поведение |
|---|---|
| `Prefab(name="Prefab", guid=None, base_guid=None, menu_path=None)` | Новый контейнер; guid авто-`uuid4`, если не задан. Заданный `base_guid` ⇒ вариант. |
| `Prefab.create_via_code(name, build=None, menu_path=None, guid=None) -> Prefab` | Строит `roots_data` из callable `build(prefab)`, возвращающего список dict сущностей. |
| `prefab.capture(entities)` | Сериализует сущности в `roots_data`: хранит `source_id`, убирает живые ссылки `parent`, рекурсивно вкладывает детей, счищает `prefab_guid`. |
| `prefab.save(path)` | Пишет JSON `{guid, name, roots, base_guid?, menu_path?}`; релятивизует пути ресурсов к корню проекта. Лог `Prefab saved: path`. |
| `Prefab.load(path) -> Prefab \| None` | Читает файл обратно (понимает и legacy-формы `entity`/`entities`). При ошибке лог и `None`. |
| `prefab.instantiate(scene, registry, parent=None) -> list[Entity]` | Глубоко копирует корни, назначает **свежие UUID каждому узлу**, десериализует, штампует `_prefab_guid`/`_prefab_source_id`, `scene.add_entity`, подвешивает к `parent`. Возвращает корневые сущности. |
| `prefab.instantiate_plain(scene, registry, parent=None) -> list[Entity]` | То же, что `instantiate`, затем `Prefab.unpack` каждого корня — простые копии без связи. |
| `Prefab.unpack(entity_root)` | Счищает `_prefab_guid`/`_prefab_source_id` у корня и **всех потомков** (статический, по месту). |
| `Prefab.create_variant(base_prefab, name, guid=None) -> Prefab` | Вариант с общими данными базы и своим guid + `base_guid`. |

Импорт: `from core.ecs.prefabp import Prefab` (там же `PrefabOverrideType`, `PrefabLibrary`).

## Пять типов оверрайдов

Значения `PrefabOverrideType` и функции их вычисления/применения (лежат в `overrides.py`, прицеплены как статики `Prefab.*`):

| Значение типа | Смысл |
|---|---|
| `modified_property` | Отличается имя/active/tags/layer или одно `Component.property` от источника. `local_position`/`local_rotation` корневого `Transform` **исключены** (позиционирование — per-instance по дизайну). |
| `added_component` | У инстанса есть компонент, которого нет в префабе. |
| `removed_component` | У префаба есть компонент, потерянный инстансом. |
| `added_child` | Лишний дочерний объект у инстанса. |
| `removed_child` | У инстанса нет дочернего объекта префаба. |

| Сигнатура | Поведение |
|---|---|
| `Prefab.compute_overrides(entity) -> list[dict]` | Дифф инстанса против разрешённого источника (цепочки вариантов резолвятся). Для не-инстансов — `[]`. |
| `Prefab.compute_all_overrides(entities) -> list[dict]` | Рекурсивно по детям; при >4 сущностях — пулом потоков. |
| `Prefab.has_overrides(entities) -> bool` | `len(compute_all_overrides(...)) > 0`. |
| `Prefab.apply_overrides(entity_root)` | Записывает дифф инстанса обратно в `.zpep` и инвалидирует кэш библиотеки. |
| `Prefab.revert_instance(root_entity, registry)` | Восстанавливает имена, флаги, компоненты и детей из данных префаба (добавленных детей удаляет, недостающих пересоздаёт со свежими id). |

Каждый dict оверрайда несёт `entity_id`, `entity_name`, `source_id`, `type` плюс `property`/`old_value`/`new_value` (или варианты `component_type`/`child_source_id`).

## Пример фабрики врагов (полный, рабочий)

Спавн инстансов в рантайме с per-instance позиционированием (позиция оверрайдом не считается):

```python
from core.ecs import ComponentRegistry
from core.ecs.prefabp import Prefab
from core.maths.math3d import Vec3


class EnemyFactory:
    prefab_path: str = "assets/prefabs/enemy.zpep"
    spawn_gap: float = 3.0

    def on_start(self):
        self.scene = self._entity._scene
        self.prefab = Prefab.load(self.prefab_path)
        if self.prefab is None:
            Logger.error(f"EnemyFactory: cannot load {self.prefab_path}")
        self.spawned = 0

    def on_update(self, dt):
        if self.prefab is None:
            return
        if Input.GetKeyDown(KeyCode.E):
            roots = self.prefab.instantiate(
                self.scene, ComponentRegistry, parent=self._entity
            )
            for root in roots:
                root.name = f"Enemy_{self.spawned}"
                if root.transform is not None:
                    root.transform.local_position = Vec3(
                        float(self.spawned) * float(self.spawn_gap), 0.0, 0.0
                    )
            self.spawned += 1
            Logger.info(f"Spawned Enemy_{self.spawned - 1}")

    def report_overrides(self):
        for root in self.scene.get_root_entities():
            if root.is_prefab_instance:
                ovs = Prefab.compute_overrides(root)
                if ovs:
                    Logger.info(f"{root.name}: {len(ovs)} override(s)")
```

Захват префаба из живой иерархии (инструменты редактора / bootstrap):

```python
from core.ecs.prefabp import Prefab

prefab = Prefab("Enemy")
prefab.capture([template_root_entity])   # корень шаблона + потомки
prefab.save("assets/prefabs/enemy.zpep")

# связанный инстанс позже:
roots = Prefab.load("assets/prefabs/enemy.zpep").instantiate(scene, ComponentRegistry)

# ...или простая несвязанная копия:
plain = Prefab.load("assets/prefabs/enemy.zpep").instantiate_plain(scene, ComponentRegistry)
```

## Связанные страницы

- `entity-api.md` — `duplicate_entity` (копия внутри сцены) против префабов (ресурс).
- `scene-api.md` — сцены, в которых живут инстансы.
- `pitfalls.md` — позиция корня намеренно не оверрайд.

## Проверено по коду

- `core/ecs/prefabp/prefab.py:17-30` (5 значений `PrefabOverrideType` + исключения корневого трансформа), `:45-59` (create/instantiate_plain), `:61-100` (capture/instantiate), `:122-129` (ремап свежих UUID), `:131-220` (save/load), `:354-366` (unpack/variant).
- `core/ecs/prefabp/overrides.py:13-54` (compute), `:209-244` (дифф компонентов + пропуск корня), `:245-261` (has/apply), `:338-419` (revert + прицепка `Prefab.*`).
- `core/ecs/prefabp/__init__.py` (экспорты `Prefab`, `PrefabOverrideType`, `PrefabLibrary`).
