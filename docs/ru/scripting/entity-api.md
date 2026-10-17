# Entity API

Всё, что скрипт может делать с сущностями и компонентами: создание, поиск, чтение, добавление, удаление, дублирование, родительство, теги. Когда вызывается каждый метод — см. `lifecycle.md`; виджеты полей — `inspector-fields.md`.

## Поиск: сцена → сущность

| Сигнатура | Поведение |
|---|---|
| `Scene.create_entity(name="Entity", prefab_guid=None) -> Entity` | Создаёт **голую** сущность: ни одного компонента, даже `Transform`. Помечает сцену dirty. |
| `Scene.add_entity(e)` | Регистрирует собранную вручную `Entity` (индексы, апдейт-наборы, spatial). |
| `Scene.remove_entity(eid)` | Удаляет сущность **и всех детей рекурсивно**. Вызывает `on_destroy` у компонентов. |
| `Scene.get_entity(eid) -> Entity \| None` | По строковому id. |
| `Scene.get_entity_by_name(name) -> Entity \| None` | Первая сущность с таким именем (имена не уникальны). |
| `Scene.get_all_entities() -> list[Entity]` | Плоский кэшированный список. |
| `Scene.get_root_entities() -> list[Entity]` | Сущности с `parent is None` (кэш сбрасывается при смене родителя). |
| `Scene.get_entities_with_component(cls) -> list[Entity]` | Индексированный запрос, с учётом наследования. |
| `Scene.duplicate_entity(entity, new_name="") -> Entity` | Глубокая копия сущности **и всего поддерева** со свежими UUID; id костей Armature перепривязываются. |
| `scene.mark_dirty()` / `scene.mark_clean()` / `scene.dirty` | Ручной флаг изменений. |

Внутри скрипта сцена всегда под рукой: `self._entity._scene` (движок назначает до `on_awake`).

## Поиск: сущность → компонент

| Сигнатура | Поведение |
|---|---|
| `entity.add_component(comp, key=None) -> Component` | Прикрепляет; вызывает `comp.on_awake()`. Ключ по умолчанию `TypeName` или `TypeName.<8 hex>` для множественных. |
| `entity.get_component(cls) -> Component \| None` | Сначала точный тип, затем fallback через `issubclass`. Возвращает **первый** совпавший. |
| `entity.get_components(cls) -> list` | Все совпадения. |
| `entity.get_component_by_name(name) -> Component \| None` | Порядок: имя типа → точный ключ → префикс `"Name."` (мульти-инстансы) → fallback через `ComponentRegistry`. |
| `entity.has_component(cls) -> bool` | Наличие точного типа или наследника. |
| `entity.get_all_components() -> list` | Все прикреплённые компоненты. |
| `entity.get_component_in_children(cls)` | Поиск вглубь по детям. |
| `entity.remove_component(cls)` | Удаляет первое совпадение, вызывает `on_destroy`. |
| `entity.remove_all_components(cls)` | Удаляет все совпадения. |
| `entity.remove_component_by_key(key)` | Удаляет точный keyed-инстанс (для множественных компонентов). |

## Иерархия, теги, флаги

| Член | Поведение |
|---|---|
| `entity.set_parent(parent, preserve_world=True)` | Смена родителя; при `True` (по умолчанию) мировая матрица сохраняется через round-trip `world_matrix`. `None` — открепить. Кэши глубины сбрасываются для всего поддерева. |
| `entity.parent` / `entity.children` | Ссылки иерархии (только чтение). |
| `entity.transform` | Первый компонент с именем `Transform` (кэш), либо `None` у голых сущностей. |
| `entity.active: bool` | Переключение деактивирует сущность: апдейт-наборы ужимаются, spatial-запись падает, у всех включённых компонентов срабатывают `on_disable`/`on_enable`. |
| `comp.enabled: bool` | Переключатель отдельного компонента с той же проводкой. |
| `entity.tags: set[str]` | Свободный набор тегов (`entity.tags.add("enemy")`). Сериализуется списком. |
| `entity.layer: int` | Целочисленный слой, по умолчанию `0`. |
| `entity.prefab_guid` / `entity.prefab_source_id` | Связь с префабом; `None` у обычных сущностей. |
| `entity.is_prefab_instance: bool` | `True`, когда задан `prefab_guid`. |
| `entity.embed_resources` / `entity.locked` / `entity.system` | Флаги сериализации/редактора. |

Детали `duplicate_entity`: каждый узел копии получает новый `uuid4`; дети скопированных узлов перелинковываются внутри копии; узел, чей исходный родитель **вне** копии, цепляется к исходному родителю; `new_name` переименовывает корень копии. `bone_entity_ids` арматур маппятся через таблицу старый→новый id.

## Пример спавнера (полный, рабочий)

Только проверенные вызовы: голая `create_entity`, ручной `Transform`, `set_parent`, `tags`, `get_root_entities`.

```python
from core.components.transform.tr import Transform
from core.maths.math3d import Vec3


class Spawner:
    spawn_name: str = "Pickup"
    count: int = 5
    spacing: float = 2.0

    def on_start(self):
        self.scene = self._entity._scene
        self.spawned = []
        for i in range(int(self.count)):
            e = self.scene.create_entity(f"{self.spawn_name}_{i}")
            t = Transform()
            t.local_position = Vec3(float(i) * float(self.spacing), 0.0, 0.0)
            e.add_component(t)
            e.tags.add("pickup")
            e.set_parent(self._entity, preserve_world=False)
            self.spawned.append(e.id)

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.SPACE):
            e = self.scene.create_entity(self.spawn_name)
            e.add_component(Transform())
            e.tags.add("pickup")

    def alive_roots(self):
        return [e.name for e in self.scene.get_root_entities()]
```

Поля инспектора: `spawn_name` (строка), `count` (int), `spacing` (float) — обычные аннотированные атрибуты.

Дублирование в рантайме (свежие UUID гарантирует движок):

```python
class Cloner:
    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.C):
            scene = self._entity._scene
            copy = scene.duplicate_entity(self._entity, new_name="Clone")
            copy.set_parent(None, preserve_world=True)
```

Защитное чтение компонентов (безопасно для мульти-инстансов):

```python
class CacheDemo:
    def on_start(self):
        self.body = self._entity.get_component_by_name("Rigidbody")
        self.audio = self._entity.get_component_by_name("AudioSource")

    def on_update(self, dt):
        if self.body is not None and Input.GetKey(KeyCode.W):
            self.body.add_force(Vec3(0.0, 0.0, 10.0))
```

Идиома: кэшировать в `on_start`, никогда не искать каждый кадр.

## Связанные страницы

- `overview.md` — обнаружение скриптов (порядок `dir()`) и несколько скриптов на сущности.
- `lifecycle.md` — когда вокруг этих вызовов срабатывают `on_awake`/`on_start`/`on_destroy`.
- `scene-api.md` — сохранение/загрузка сцен, где живут сущности.
- `prefab-scripting.md` — спавн из префабов вместо голых сущностей.
- `pitfalls.md` — у `create_entity` нет `Transform`; `get_entity_by_name` — первое совпадение.

## Проверено по коду

- `core/ecs/entity.py:133-161` (`set_parent`), `:164-222` (`add_component`), `:223-333` (удаления), `:334-384` (get/has), `:399-407` (поиск по детям), `:408-445` (serialize/deserialize), `:69-132` (active/tags/layer/флаги префаба).
- `core/ecs/scene.py:229-322` (create/add/remove/duplicate/get/roots), `:217-218` (dirty).
- `core/ecs/scene_serialize.py:14-18` (перепривязка Armature), `:107-191` (дублирование со свежими UUID).
- `core/ecs/component.py` (проводка `enabled`, автоопределение `_updates`).
