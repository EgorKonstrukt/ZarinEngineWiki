# Разбор example-плагина

`plugins/example_plugin.py` (43 строки) — канонический минимальный плагин: все конвенции в одном месте. Прочитайте, скопируйте, переименуйте.

```python
from core.foundation.plugin_manager import PluginBase
from core.foundation.logger import Logger

class ExamplePlugin(PluginBase):
    NAME = "ExamplePlugin"
    VERSION = "1.0.0"
    DESCRIPTION = "Example user plugin demonstrating the plugin API."
    SYSTEM = False

    def __init__(self):
        super().__init__()
        self._frame = 0

    def initialize(self, engine):
        super().initialize(engine)
        self.add_menu_item("ExamplePlugin", "Say Hello", lambda: Logger.info("Hello from ExamplePlugin!"), "Ctrl+Shift+H")
        self.add_toolbar_button("Hello", lambda: Logger.info("Hello from toolbar!"), tooltip="Say hello")
        Logger.info(f"[{self.NAME}] Initialized! Counter: {self.get_config('counter', 0)}")

    def on_scene_loaded(self, scene):
        Logger.debug(f"[{self.NAME}] Scene loaded: {scene.name}")

    def on_play_start(self):
        Logger.debug(f"[{self.NAME}] Play started!")
        self._frame = 0

    def on_play_stop(self):
        Logger.debug(f"[{self.NAME}] Play stopped after {self._frame} frames.")
        counter = self.get_config("counter", 0) + 1
        self.set_config("counter", counter)
        Logger.info(f"[{self.NAME}] Run counter saved: {counter}")

    def shutdown(self):
        Logger.info(f"[{self.NAME}] Shutdown.")

def get_plugin():
    return ExamplePlugin()
```

Уроки построчно:

- Блок метаданных (`NAME/VERSION/DESCRIPTION/SYSTEM`) — копировать и переименовать; `SYSTEM = False` держит в пользовательском тире.
- `super().__init__()`, затем `super().initialize(engine)` — второй вызов загружает сохранённый конфиг; без него `get_config` всегда отдаёт дефолты.
- `add_menu_item(section, label, callback, shortcut)` — место в меню с хоткеем; `add_toolbar_button(label, callback, tooltip)` — доступ в клик. Колбэки-однострочники — лямбдами.
- `on_scene_loaded(scene)` читает `scene.name` — хуки сцен получают живой объект, опрашивайте напрямую.
- `on_play_start` сбрасывает персистентное состояние (`_frame = 0`); `on_play_stop` инкрементит и persistит `counter` — канонический round-trip конфига в четыре строки.
- `shutdown` логирует; базовый shutdown (здесь beyond лога не тронут) сохраняет конфиг и откатывает патчи.
- `get_plugin()` уровня модуля возвращает свежий инстанс — менеджер импортирует модуль и зовёт ровно это. Любое другое имя — и файл невидим.

Упражнения: смените шорткат и смотрите обновление меню; добавьте `step()` со счётом кадров в `_frame`, чтобы stop-сообщение показывало реальные числа; храните второй ключ (`last_scene`) в `on_scene_loaded` и печатайте в `initialize`.

Связанное: жизненный цикл PluginBase (все хуки), гид пользовательских (куда класть файл), менеджер плагинов (как грузит).
