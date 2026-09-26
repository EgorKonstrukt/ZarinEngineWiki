# Plugins Overview

Plugins extend the editor and the engine without touching core files: dock panels, toolbar buttons, menu items, new components, file openers, method patches, extra Inspector fields, native extensions.

Two flavors:

- System plugins (`SYSTEM = True`, shipped in `plugins/`): physics, network transport, mesh editor, VR, MCP bridge — the engine relies on them, they load first and cannot be casually removed.
- User plugins (`SYSTEM = False`, live in `plugins/user/`): team tools, importers, level utilities, experiment rigs — load after system plugins, enable/disable freely.

Three formats, one manager:

- `.py` source plugins with a `get_plugin()` factory returning the instance.
- Native `.dll` / `.so` shared libraries with a Python entry shim.
- `.zplugin` signed packages: manifest + code + native modules + bundled libs, verified against `config/trusted_keys`, extracted once and cached by sha256 fingerprint.

`PluginManager` (`core/foundation/plugin_manager.py`, ~1700 lines) owns discovery, `_load_order`, activation, dependency checks from manifests, security verification, config persistence per plugin, and teardown in reverse order.

Pages: PluginBase Lifecycle, Plugin Manager, Example Plugin walkthrough, User Plugins guide, .zplugin Packaging, System Plugins catalog, Network Plugin, VR Plugin, MCP Bridge, Music/Plotter/QtQuick extensions.

Five-minute first plugin: copy `plugins/example_plugin.py` into `plugins/user/my_plugin.py`, rename the class and `NAME`, keep `get_plugin()` at the bottom, restart the editor — the menu item appears, the toolbar button appears, the run counter persists across sessions.
