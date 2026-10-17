# Scene API

Создание, сохранение и загрузка сцен из кода. Операции уровня сущностей — в `entity-api.md`; спавн из префабов — в `prefab-scripting.md`.

## Главный факт

`Scene.create_entity()` возвращает **голую** сущность без компонентов. Если она должна быть видна или placed в мире — добавьте `Transform` сами:

```python
from core.components.transform.tr import Transform

e = scene.create_entity("Prop")
e.add_component(Transform())
```

## Сохранение/загрузка через движок (синхронно)

Методы миксина сцен движка, блокируют до завершения:

| Сигнатура | Поведение |
|---|---|
| `engine.load_scene(path) -> Scene \| None` | Читает JSON, извлекает встроенные ресурсы, резолвит относительные пути, десериализует, ставит `scene.path` и `scene.name` (из имени файла), помечает чистой, уведомляет плагины. При ошибке — `None`. |
| `engine.save_scene(path=None) -> None` | Сериализует, встраивает ресурсы, релятивизует пути, ротирует бэкапы (`path.bak`, `path.bak2`), пишет атомарно (`path.build` → rename), помечает чистой. По умолчанию — `scene.path`. |
| `engine.save_scene_object(scene, path=None) -> bool` | То же, но для явно переданной сцены; возвращает успех. |
| `engine.load_scene_from_data(data) -> Scene \| None` | Устанавливает активной уже готовый dict (путь сетевой синхронизации). |

## Сохранение/загрузка через движок (асинхронно)

Неблокирующие варианты для редактора и экранов загрузки; колбэк выполняется в GUI-потоке:

| Сигнатура | Поведение |
|---|---|
| `engine.load_scene_async(path, data=None, on_done=None)` | Worker-поток читает/парсит/десериализует, затем подмена в GUI-потоке и вызов `on_done(scene)`. Прогресс `scene:load` с троттлингом. |
| `engine.save_scene_async(path=None, on_done=None)` | Снапшот через `scene.serialize()`, сохранение в worker-потоке, затем clean и `on_done(ok)`. |

`on_done` — любой callable: `on_done(scene)` для загрузок, `on_done(ok: bool)` для сохранений.

## Сериализация/восстановление сцены

| Сигнатура | Поведение |
|---|---|
| `Scene.serialize() -> dict` | `{"name": ..., "entities": {eid: entity_dict}}` плюс флаги `embed_all`/`compress_resources`, если заданы. Сцены больше 64 сущностей сериализуются пулом потоков. |
| `Scene.deserialize(data, registry) -> Scene` | Перестраивает сущности, перелинковывает родителей по сохранённым id, перерегистрирует индексы/апдейт-наборы, сбрасывает dirty. |
| `scene.mark_dirty()` / `scene.mark_clean()` | Флаг изменений + bump версии рендера при dirty. |
| `scene.path` / `scene.name` / `scene.dirty` | Путь файла, отображаемое имя, флаг несохранённых изменений. |

## Пример загрузчика уровней (полный, рабочий)

Скрипт меню, грузящий уровни асинхронно без фризов, только проверенные сигнатуры:

```python
class LevelLoader:
    level_path: str = "assets/scenes/level_02.zpes"

    def on_start(self):
        from core.engine.eng import Engine
        self.engine = Engine.instance()
        self.loading = False

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.F5) and not self.loading:
            self.loading = True
            Logger.info(f"Loading {self.level_path} ...")
            self.engine.load_scene_async(
                self.level_path,
                on_done=self._on_loaded,
            )

    def _on_loaded(self, scene):
        self.loading = False
        if scene is None:
            Logger.error(f"Failed to load {self.level_path}")
            return
        Logger.info(f"Loaded scene: {scene.name} ({len(scene.get_all_entities())} entities)")

    def on_update_save(self, dt):
        if Input.GetKeyDown(KeyCode.F6):
            self.engine.save_scene_async(on_done=lambda ok: Logger.info(f"Saved: {ok}"))
```

Синхронное сохранение с явным путём (инструменты редактора / bootstrap, не каждый кадр):

```python
from core.engine.eng import Engine

engine = Engine.instance()
engine.save_scene("assets/scenes/checkpoint.zpes")
scene = engine.load_scene("assets/scenes/checkpoint.zpes")
assert scene is not None and not scene.dirty
```

Круговая проверка снапшота в памяти (проверенная пара `serialize`/`deserialize`):

```python
from core.ecs import Scene, ComponentRegistry

snapshot = scene.serialize()
clone = Scene.deserialize(snapshot, ComponentRegistry)
assert len(clone.get_all_entities()) == len(scene.get_all_entities())
```

## Связанные страницы

- `entity-api.md` — что лежит внутри сцены.
- `prefab-scripting.md` — спавн инстансов префабов в загруженную сцену.
- `hot-reload.md` — состояние сцены перезагрузкой скриптов не трогается.
- `debugging.md` — ошибки load/save видны через `Logger.error`.

## Проверено по коду

- `core/engine/eng/scenes.py:76-110` (`save_scene_async`), `:111-175` (`load_scene_async` + подмена), `:332-365` (`load_scene`), `:366-391` (`load_scene_from_data`), `:392-416` (`save_scene`), `:417-443` (`save_scene_object`).
- `core/ecs/scene.py:229-239` (голая `create_entity`), `:361-414` (serialize/deserialize/кэши), `:217-218` (флаг dirty).
