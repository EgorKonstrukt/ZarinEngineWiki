# Example Plugin Walkthrough

`plugins/example_plugin.py` (43 lines) is the canonical minimal plugin — every convention in one place. Read it, copy it, rename it.

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

Line-by-line lessons:

- Metadata block (`NAME/VERSION/DESCRIPTION/SYSTEM`) — copy and rename; `SYSTEM = False` keeps it in the user tier.
- `super().__init__()` then `super().initialize(engine)` — the second call loads the persisted config; without it `get_config` always returns defaults.
- `add_menu_item(section, label, callback, shortcut)` — menu placement with a hotkey; `add_toolbar_button(label, callback, tooltip)` — one-click access. Callbacks can be lambdas for one-liners.
- `on_scene_loaded(scene)` reads `scene.name` — scene hooks receive the live object, query it directly.
- `on_play_start` resets per-run state (`_frame = 0`); `on_play_stop` increments and persists the `counter` — the canonical config round-trip in four lines.
- `shutdown` logs; base shutdown (not overridden here beyond logging) saves config and unpatches.
- Module-level `get_plugin()` returns a fresh instance — the manager imports the module and calls exactly this. Any name else and the file is invisible.

Exercises: change the shortcut and watch the menu update; add `step()` counting frames into `_frame` so the stop message reports real numbers; store a second key (`last_scene`) in `on_scene_loaded` and print it in `initialize`.

Related: PluginBase Lifecycle (all hooks), User Plugins guide (where to put the file), Plugin Manager (how it loads).
