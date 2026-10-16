# Collaboration and MCP Docks

> Live teamwork surface: `CollaborationPanel` (`editor/panels/collab/`) drives host/join/relay/invite flows on `CollaborationManager`; the MCP side (`plugins/zarin_mcp/`) exposes the editor to AI agents via `McpServer` + `ZarinMCPPanel` dock.

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `CollaborationPanel` | `(engine, parent=None)` + `set_collaboration_manager(mgr)` | `collab/panel.py` dock; mixes actions + status |
| Host/join | `_on_host()`, `_on_connect()`, `_on_disconnect()`, `_on_auth_failed(reason)` | Direct TCP session |
| Relay | `_on_host_relay()`, `_on_join_relay()`, `_on_relay_server_toggle(checked)` | Relay-mediated session |
| Invites | `_on_copy_invite()`, `_on_join_invite()` | `make_invite`/`parse_invite` codes |
| Options | `_on_upnp_toggle(checked)`, `_on_play_toggle(checked)`, `_on_sync_assets()` | UPnP mapping, shared play state, asset push |
| Status | `_update_status()`, `_refresh_peers()`, `_update_asset_sync()`, `_update_upnp_label()`, `_format_bytes(b)` | Peer list + transfer readouts |
| Scene apply | `_on_scene_sync(scene_data: dict) -> bool` | Incoming snapshot gate |
| `ZarinMCPPlugin` | `NAME="ZarinMCP"`, `VERSION="2.1.0"`, `SYSTEM=False` + `initialize(engine)`, `shutdown()` | `plugins/zarin_mcp/__init__.py`, `get_plugin()` factory |
| `McpServer` | `plugins/zarin_mcp/mcp_server.py` | MCP protocol server |
| `Registry` | `plugins/zarin_mcp/registry.py` | Tool registry |
| `ZarinMCPPanel` | `QWidget` in `plugins/zarin_mcp/mcp_dock.py` | Dock UI for the bridge |
| `_MainThreadDispatcher` | `QObject` in `plugins/zarin_mcp/main_thread.py` | Marshals agent calls onto the Qt thread |
| Config | `zarin_mcp_config.json` (repo root), `zarin_mcp_launcher.py` | Bridge config + launcher |

## Usage in Editor

Collaboration dock: enter name/room/password → Host (optionally via relay or with UPnP toggle) or Connect/Join-via-invite; peers appear with colors; scene snapshots, asset sync and script cursors flow automatically; `_on_play_toggle` shares Play state. MCP dock: start the bridge, inspect registered tools, see agent activity; agent edits land through the main-thread dispatcher so the UI never touches Qt off-thread.

## Usage in Code

```python
panel.set_collaboration_manager(engine.collaboration)
panel._on_host()
panel._on_copy_invite()   # copies a join code for peers
```

## Related

- Networking: Collaboration, Transport and Protocol; `core/network/collab/manager.py` (`start_server/connect/host_relay/join_relay/create_invite/join_invite`)
- Plugins: MCP Bridge, PluginBase Lifecycle; `plugins/zarin_mcp/handlers/`

## Verified against

- `editor/panels/collab/panel.py`, `editor/panels/collab/actions.py`, `editor/panels/collab/status.py`
- `editor/main_window/widgetp/toolbar_host.py` (collab scene/script tabs)
- `plugins/zarin_mcp/__init__.py`, `plugins/zarin_mcp/mcp_server.py`, `plugins/zarin_mcp/registry.py`, `plugins/zarin_mcp/mcp_dock.py`, `plugins/zarin_mcp/main_thread.py`
- `zarin_mcp_config.json`, `zarin_mcp_launcher.py`
