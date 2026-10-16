# Tutorial: First Plugin

A working editor plugin in 5 steps: subclass `PluginBase`, export `get_plugin()`, react to play/scene hooks, install under `plugins/user/`. API from `core/foundation/plugins/base.py`; the example below mirrors `plugins/example_plugin.py` and the `plugins/user/bsp_import/` package layout.

Available hooks: `initialize(engine)`, `shutdown()`, `step(dt)` / `pre_step(dt)`, `on_viewport_ready(viewport)`, `on_scene_loaded(scene)` / `on_scene_unloaded(scene)`, `on_project_opened()`, `on_play_start()` / `on_play_stop()`, `on_main_window_ready(main_window)`. UI helpers: `add_menu_item(menu, text, callback, shortcut, icon)`, `add_toolbar_button(text, callback, icon, tooltip)`, `register_dock(title, factory, area, tab_group, icon)`, `register_file_opener(extensions, handler, label)`, `register_component(cls)`. Config persists as JSON via `get_config(key, default)` / `set_config(key, value)`.

## Steps

1. **Create the package.** Folder `plugins/user/hello_plugin/` with `__init__.py` (same layout as `plugins/user/bsp_import/`: package dir + `__init__.py` re-export; discovery picks up user-dir packages, warning on name override).
2. **Paste the code** below into `plugins/user/hello_plugin/__init__.py`.
3. **Restart the editor** (or reload plugins). The plugin's `initialize` runs: menu `HelloPlugin → Say Hello` (`Ctrl+Shift+H`) and a `Hello` toolbar button appear.
4. **Open any scene** → `on_scene_loaded` logs the scene name. **Press Play** → `on_play_start` logs and resets the frame counter; move around; **Stop** → `on_play_stop` increments the persistent `counter` in `config/plugins/HelloPlugin.json`.
5. **Extend:** add `register_dock("Hello", factory, area="left")` for a panel, or `register_component(MyComp)` (must inherit `core.ecs.Component`) to ship a component with the plugin.

## Full code — `plugins/user/hello_plugin/__init__.py`

```python
from core.foundation.plugins import PluginBase
from core.foundation.logger import Logger


class HelloPlugin(PluginBase):
    NAME = "HelloPlugin"
    VERSION = "1.0.0"
    DESCRIPTION = "My first ZarinEngine plugin."
    SYSTEM = False  # user plugin, not engine system

    def __init__(self):
        super().__init__()
        self._frame = 0

    def initialize(self, engine):
        super().initialize(engine)  # loads config/HelloPlugin.json
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

Rules the loader enforces: class attributes `NAME` / `VERSION` / `DESCRIPTION` / `SYSTEM`; module-level `get_plugin()` factory returning the instance; `super().initialize(engine)` first (loads config), `super().shutdown()` last (saves config, restores patches).

## Verified against

`core/foundation/plugins/base.py`, `core/foundation/plugins/manager.py`, `plugins/example_plugin.py`, `plugins/user/bsp_import/__init__.py`.
