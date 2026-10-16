# Plugins

Extending the engine without touching core — `PluginBase` lifecycle, manager, packaging, catalog. Read from `core/foundation/plugins/` (`base.py`, `manager.py`, `discovery.py`, `loader.py`) and `plugins/`.

- Plugins Overview — system vs user plugins, three formats, first-plugin recipe.
- PluginBase Lifecycle — metadata, hooks, UI/patch/config capabilities.
- Plugin Manager — discovery, load order, verification, teardown.
- Example Plugin walkthrough — minimal working plugin.
- User Plugins guide — `plugins/user/` workflow.
- .zplugin Packaging — signed packages, fingerprints, trust config.
- System Plugins catalog — shipped plugins that load first.
- Network Plugin — multiplayer transport plugin.
- VR Plugin — VR dock and components.
- MCP Bridge — AI-agent bridge (`ZarinMCP`).
- Music/Plotter/QtQuick extensions — tracker music, plotter, QtQuick world plugins.
