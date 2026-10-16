# Доки коллаборации и MCP

> Поверхность живой командной работы: `CollaborationPanel` (`editor/panels/collab/`) управляет флоу host/join/relay/invite на `CollaborationManager`; сторона MCP (`plugins/zarin_mcp/`) открывает редактор AI-агентам через `McpServer` + док `ZarinMCPPanel`.

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| `CollaborationPanel` | `(engine, parent=None)` + `set_collaboration_manager(mgr)` | Док `collab/panel.py`; миксины actions + status |
| Хост/вход | `_on_host()`, `_on_connect()`, `_on_disconnect()`, `_on_auth_failed(reason)` | Прямая TCP-сессия |
| Relay | `_on_host_relay()`, `_on_join_relay()`, `_on_relay_server_toggle(checked)` | Сессия через релей |
| Инвайты | `_on_copy_invite()`, `_on_join_invite()` | Коды `make_invite`/`parse_invite` |
| Опции | `_on_upnp_toggle(checked)`, `_on_play_toggle(checked)`, `_on_sync_assets()` | UPnP-маппинг, общий Play, пуш ассетов |
| Статус | `_update_status()`, `_refresh_peers()`, `_update_asset_sync()`, `_update_upnp_label()`, `_format_bytes(b)` | Список пиров + показания передач |
| Применение сцены | `_on_scene_sync(scene_data: dict) -> bool` | Гейт входящих снапшотов |
| `ZarinMCPPlugin` | `NAME="ZarinMCP"`, `VERSION="2.1.0"`, `SYSTEM=False` + `initialize(engine)`, `shutdown()` | `plugins/zarin_mcp/__init__.py`, фабрика `get_plugin()` |
| `McpServer` | `plugins/zarin_mcp/mcp_server.py` | Сервер протокола MCP |
| `Registry` | `plugins/zarin_mcp/registry.py` | Реестр инструментов |
| `ZarinMCPPanel` | `QWidget` в `plugins/zarin_mcp/mcp_dock.py` | UI дока моста |
| `_MainThreadDispatcher` | `QObject` в `plugins/zarin_mcp/main_thread.py` | Маршалинг вызовов агентов в Qt-поток |
| Конфиг | `zarin_mcp_config.json` (корень репо), `zarin_mcp_launcher.py` | Конфиг моста + лаунчер |

## Usage in Editor

Док коллаборации: имя/комната/пароль → Host (опционально через relay или с тогглом UPnP) либо Connect/вход по инвайту; пиры появляются с цветами; снапшоты сцен, синк ассетов и курсоры скриптов текут автоматически; `_on_play_toggle` расшаривает состояние Play. Док MCP: запуск моста, осмотр зарегистрированных инструментов, активность агентов; правки агентов идут через main-thread dispatcher, так что UI никогда не трогает Qt вне потока.

## Usage in Code

```python
panel.set_collaboration_manager(engine.collaboration)
panel._on_host()
panel._on_copy_invite()   # копирует код входа для пиров
```

## Related

- Сеть: Collaboration, Transport and Protocol; `core/network/collab/manager.py` (`start_server/connect/host_relay/join_relay/create_invite/join_invite`)
- Плагины: MCP Bridge, PluginBase Lifecycle; `plugins/zarin_mcp/handlers/`

## Verified against

- `editor/panels/collab/panel.py`, `editor/panels/collab/actions.py`, `editor/panels/collab/status.py`
- `editor/main_window/widgetp/toolbar_host.py` (коллаб-вкладки сцен/скриптов)
- `plugins/zarin_mcp/__init__.py`, `plugins/zarin_mcp/mcp_server.py`, `plugins/zarin_mcp/registry.py`, `plugins/zarin_mcp/mcp_dock.py`, `plugins/zarin_mcp/main_thread.py`
- `zarin_mcp_config.json`, `zarin_mcp_launcher.py`
