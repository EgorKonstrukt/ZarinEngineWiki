# Урок: первый плагин

Рабочий плагин редактора за 5 шагов: наследник `PluginBase`, экспорт `get_plugin()`, реакция на хуки play/сцены, установка в `plugins/user/`. API — из `core/foundation/plugins/base.py`; пример ниже повторяет `plugins/example_plugin.py` и раскладку пакета `plugins/user/bsp_import/`.

Доступные хуки: `initialize(engine)`, `shutdown()`, `step(dt)` / `pre_step(dt)`, `on_viewport_ready(viewport)`, `on_scene_loaded(scene)` / `on_scene_unloaded(scene)`, `on_project_opened()`, `on_play_start()` / `on_play_stop()`, `on_main_window_ready(main_window)`. UI-хелперы: `add_menu_item(menu, text, callback, shortcut, icon)`, `add_toolbar_button(text, callback, icon, tooltip)`, `register_dock(title, factory, area, tab_group, icon)`, `register_file_opener(extensions, handler, label)`, `register_component(cls)`. Конфиг хранится как JSON через `get_config(key, default)` / `set_config(key, value)`.

## Шаги

1. **Создайте пакет.** Папка `plugins/user/hello_plugin/` с `__init__.py` (та же раскладка, что у `plugins/user/bsp_import/`: директория пакета + `__init__.py` с реэкспортом; дискавери подхватывает пакеты user-директории, о конфликте имён предупреждает).
2. **Вставьте код** ниже в `plugins/user/hello_plugin/__init__.py`.
3. **Перезапустите редактор** (или перезагрузите плагины). Отработает `initialize`: появятся меню `HelloPlugin → Say Hello` (`Ctrl+Shift+H`) и кнопка тулбара `Hello`.
4. **Откройте любую сцену** → `on_scene_loaded` залогирует имя сцены. **Нажмите Play** → `on_play_start` залогирует и сбросит счётчик кадров; подвигайтесь; **Stop** → `on_play_stop` увеличит персистентный `counter` в `config/plugins/HelloPlugin.json`.
5. **Развивайте:** добавьте `register_dock("Hello", factory, area="left")` для панели или `register_component(MyComp)` (обязан наследовать `core.ecs.Component`), чтобы поставлять компонент вместе с плагином.

## Полный код — `plugins/user/hello_plugin/__init__.py`

```python
from core.foundation.plugins import PluginBase
from core.foundation.logger import Logger


class HelloPlugin(PluginBase):
    NAME = "HelloPlugin"
    VERSION = "1.0.0"
    DESCRIPTION = "My first ZarinEngine plugin."
    SYSTEM = False  # пользовательский плагин, не системный

    def __init__(self):
        super().__init__()
        self._frame = 0

    def initialize(self, engine):
        super().initialize(engine)  # грузит config/HelloPlugin.json
        self.add_menu_item("HelloPlugin", "Say Hello",
                           lambda: Logger.info("Hello from HelloPlugin!"),
                           "Ctrl+Shift+H")
        self.add_toolbar_button("Hello",
                                lambda: Logger.info("Hello from toolbar!"),
                                tooltip="Say hello")
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
        super().shutdown()


def get_plugin():
    return HelloPlugin()
```

Правила, которые требует загрузчик: атрибуты класса `NAME` / `VERSION` / `DESCRIPTION` / `SYSTEM`; фабрика `get_plugin()` на уровне модуля, возвращающая инстанс; `super().initialize(engine)` первым (грузит конфиг), `super().shutdown()` последним (сохраняет конфиг, откатывает патчи).

## Проверено по коду

`core/foundation/plugins/base.py`, `core/foundation/plugins/manager.py`, `plugins/example_plugin.py`, `plugins/user/bsp_import/__init__.py`.
