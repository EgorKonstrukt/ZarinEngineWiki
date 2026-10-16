# Каталог системных плагинов

Поставляются в `plugins/` (сверены `NAME` / `VERSION` / `SYSTEM` по каждому файлу с `get_plugin()`; нюанс: в каталоге смешаны тир движка `SYSTEM = True` и bundled-инструменты `SYSTEM = False` — тир определяется на плагин, а не на папку). По странице двум сверенным; капсулы остальным.

| Плагин (`NAME`) | Версия | `SYSTEM` | Файл(ы) (есть `get_plugin()`) | Роль |
|---|---|---|---|---|
| NetworkPlugin | 1.0.0 | True | `network_plugin.py` | Геймплейная обвязка транспорта: host/connect, хендлеры, покадровый poll. См. страницу сетевого плагина. |
| PhysicsPlugin | 0.4.0 | True | `physics_plug/plugin.py` (+ реэкспорт `physics_plug/__init__.py`) | Режимы солвера (single/multi/per-layer), фоновый процесс, синк сцены. См. солвер и потоки. |
| PhysicsVisualisation | 1.1.0 | True | `physviz/plugin.py` | Отладочная отрисовка форм и цветов слоёв. |
| QtQuickWorldPlugin | 1.0.0 | True | `qt_quick_plugin/plug/plugin.py` | Хостинг QtQuick-поверхностей внутри редактора. |
| VRPlugin | 1.0.0 | True | `vr_plugin/plug/plugin.py` + `vr_plugin/vr_dock.py` + `vr_plugin/components/` | VR-рантайм ядро плюс док редактора. См. страницу VR. |
| ZarinMCP | 2.1.0 | False | `zarin_mcp/__init__.py` (server, dock, registry, handlers) | MCP-сервер, выставляющий инструменты движка AI-агентам. См. страницу MCP. |
| PlotterPlugin | 1.2.0 | False | `plotter_plugin/__init__.py` | Панели графиков кривых/данных. |
| TrackerMusicPlugin | 1.0.0 | False | `tracker_music_plugin/__init__.py` | Проигрывание трекерной музыки (движок rend) для ретро-скоров. |
| ExamplePlugin | 1.0.0 | False | `example_plugin.py` (43 строки) | Канонический минимальный плагин. См. разбор. |
| MeshEditorPlugin | 1.0.0 | False | `mesh_editor_plugin.py` | Внутриредакторное моделирование в стиле ProBuilder и операции. |
| PhysicsDragPlugin | 0.1.0 | False | `physics_drag_plugin.py` | Таскание rigidbody мышью во вьюпорте редактора. |
| BSPImport | 2.0.0 | False | `user/bsp_import/plugin.py` | Пользовательский пример импорта BSP. |

Системные регистрируют компоненты, доки и панели ровно как пользовательские (тот же базовый класс, те же хуки) — разница лишь в тире загрузки и опоре движка. Отключение физики или сети деградирует matching подсистему; панель менеджера предупреждает заранее.

`.pending/` держит staged плагины до активации; `mcp_pack-2.1.1.zplugin` — шиппабельный MCP-артефакт (см. упаковку).

Связанное: менеджер плагинов (тиры), сетевой плагин, VR-плагин, MCP-мост, страница музыка/плоттер/QtQuick.
