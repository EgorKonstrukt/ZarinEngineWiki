# Жизненный цикл PluginBase

Каждый плагин — подкласс `PluginBase` (`core/foundation/plugin_manager.py`), конструируется через фабрику `get_plugin()` уровня модуля. Сначала метаданные, затем поведение.

```python
from core.foundation.plugin_manager import PluginBase
from core.foundation.logger import Logger


class GreetingPlugin(PluginBase):
    NAME = "GreetingPlugin"
    VERSION = "1.0.0"
    DESCRIPTION = "Greets the user and counts Play sessions."
    SYSTEM = False

    def __init__(self):
        super().__init__()
        self._frame = 0

    def initialize(self, engine):
        super().initialize(engine)
        self.add_menu_item("GreetingPlugin", "Say Hello", self.hello, "Ctrl+Shift+H")
        self.add_toolbar_button("Hello", self.hello, tooltip="Say hello")

    def hello(self):
        Logger.info("Hello from GreetingPlugin!")

    def on_play_start(self):
        self._frame = 0

    def on_play_stop(self):
        total = self.get_config("runs", 0) + 1
        self.set_config("runs", total)

    def shutdown(self):
        Logger.info("GreetingPlugin shutdown.")


def get_plugin():
    return GreetingPlugin()
```

Метаданные: `NAME` (ключ менеджера и лог-тег), `VERSION`, `DESCRIPTION`, `SYSTEM` (тир загрузки). Менеджер ключеет всё — логи, конфиги, порядок — от `NAME`, так что переименования сиротят старые конфиги.

Хуки жизненного цикла в порядке движка:

| Хук | Сигнатура | Когда |
|---|---|---|
| `initialize` | `(engine)` | Один раз при загрузке; всегда сначала `super()` (загружает сохранённый конфиг) |
| `shutdown` | `()` | При выгрузке; базовая реализация сохраняет конфиг и откатывает патчи |
| `step` | `(dt)` | Каждый кадр (масштабированное время) |
| `pre_step` | `(dt)` | Каждый fixed-шаг, до обновления сцены |
| `on_viewport_ready` | `(viewport)` | Создан вьюпорт редактора |
| `on_main_window_ready` | `(main_window)` | Главное окно собрано — можно доки |
| `on_project_opened` | `()` | Известен корень проекта |
| `on_scene_loaded` / `on_scene_unloaded` | `(scene)` | Границы смены сцены |
| `on_play_start` / `on_play_stop` | `()` | Границы сессии — сброс персистентного состояния здесь |

Возможности (все сверены на базовом классе):

- UI: `add_menu_item(section, label, callback, shortcut)`, `add_toolbar_button(label, callback, tooltip)`; дескрипторы доков в `self._docks`; открыватели файлов в `self._file_openers`; новые типы компонентов в `self._components`.
- Патчинг без форков: `patch_component(name, method_patches, extra_inspector_fields, filter_extensions, wraps)`, `add_inspector_field(component, field)`, `extend_file_filter(component, field, filter)`, `patch_function(module, name, wrapper)` — всё трекается PatchTracker и откатывается `unpatch_all()` (автоматически в базовом `shutdown`).
- Конфиг: `get_config(key, default)` / `set_config(key, value)` с персистентностью между рестартами; доступ к движку `self.engine`; к менеджеру `self._manager`; флаг `enabled` для мягкого вкл/выкл.
- Нативная сторона: `_native_plugin_path` находит бинарник; `resource_dir` резолвит соседнюю папку `_resources`; `bundled_library_dir` выставляет shipped-зависимости.

Правила: никогда не блокируйте `initialize` (тяжёлое отложите на хуки проекта/сцены); всегда `super().initialize(engine)`, иначе конфиг не загрузится; откатывайте свои патчи, хоть shutdown и страхует; гейтите покадровую работу флагом `enabled`.

Связанное: менеджер плагинов, разбор example-плагина, гид пользовательских.
