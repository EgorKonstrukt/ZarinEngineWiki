# PluginBase Lifecycle

Every plugin subclasses `PluginBase` (`core/foundation/plugin_manager.py`) and is constructed through the module-level `get_plugin()` factory. Metadata first, behavior second.

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

Metadata: `NAME` (manager key and log tag), `VERSION`, `DESCRIPTION`, `SYSTEM` (load tier). The manager keys everything — logs, config files, load order — off `NAME`, so renames orphan old configs.

Lifecycle hooks, in engine order:

| Hook | Signature | When |
|---|---|---|
| `initialize` | `(engine)` | Once at load; always call `super()` first (loads persisted config) |
| `shutdown` | `()` | At unload; base implementation saves config and restores all patches |
| `step` | `(dt)` | Every frame (scaled time) |
| `pre_step` | `(dt)` | Every fixed step, before scene update |
| `on_viewport_ready` | `(viewport)` | Editor viewport created |
| `on_main_window_ready` | `(main_window)` | Main window assembled — safe to add docks |
| `on_project_opened` | `()` | Project root known |
| `on_scene_loaded` / `on_scene_unloaded` | `(scene)` | Scene switch boundaries |
| `on_play_start` / `on_play_stop` | `()` | Session boundaries — reset per-run state here |

Capabilities (all verified on the base class):

- UI: `add_menu_item(section, label, callback, shortcut)`, `add_toolbar_button(label, callback, tooltip)`; dock descriptors in `self._docks`; file openers in `self._file_openers`; new component types in `self._components`.
- Patching without forks: `patch_component(name, method_patches, extra_inspector_fields, filter_extensions, wraps)`, `add_inspector_field(component, field)`, `extend_file_filter(component, field, filter)`, `patch_function(module, name, wrapper)` — everything tracked by a PatchTracker and reverted by `unpatch_all()` (automatic in base `shutdown`).
- Config: `get_config(key, default)` / `set_config(key, value)` persisted per plugin across restarts; engine access via `self.engine`; manager via `self._manager`; `enabled` flag for soft on/off.
- Native side: `_native_plugin_path` locates the binary; `resource_dir` resolves the sibling `_resources` folder; `bundled_library_dir` exposes shipped dependencies.

Rules: never block `initialize` (defer heavy work to project/scene hooks); always `super().initialize(engine)` or config never loads; undo every patch you own even though shutdown covers you; gate per-frame work on `self.enabled`.

Related: Plugin Manager, Example Plugin walkthrough, User Plugins guide.
