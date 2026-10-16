# Плагины

Расширение движка без правок ядра — жизненный цикл `PluginBase`, менеджер, упаковка, каталог. Прочитано из `core/foundation/plugins/` (`base.py`, `manager.py`, `discovery.py`, `loader.py`) и `plugins/`.

- Обзор плагинов — системные vs пользовательские, три формата, рецепт первого плагина.
- PluginBase Lifecycle — метаданные, хуки, возможности UI/патчей/конфига.
- Plugin Manager — обнаружение, порядок загрузки, верификация, разборка.
- Example Plugin walkthrough — минимальный рабочий плагин.
- User Plugins guide — флоу `plugins/user/`.
- .zplugin Packaging — подписанные пакеты, отпечатки, конфиг доверия.
- System Plugins catalog — комплектные плагины, грузятся первыми.
- Network Plugin — плагин мультиплеерного транспорта.
- VR Plugin — VR-док и компоненты.
- MCP Bridge — мост для AI-агентов (`ZarinMCP`).
- Music/Plotter/QtQuick extensions — трекерная музыка, плоттер, QtQuick-мировые плагины.
