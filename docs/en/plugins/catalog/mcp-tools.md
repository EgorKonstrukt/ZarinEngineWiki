# MCP Tools Bridge (ZarinMCP)

LLM bridge: a Model Context Protocol server inside the editor that exposes scene, components, assets, and viewport capture as tools, resources, templates, and prompts.

| NAME | VERSION | SYSTEM |
|---|---|---|
| `ZarinMCP` | `2.1.0` | `False` |

Declared in `plugins/zarin_mcp/__init__.py` (`ZarinMCPPlugin`). Registers dock `"ZarinMCP"` (area `right`), menu item `ZarinMCP → Open MCP Panel`, and an `MCP` toolbar button.

## Files

- `plugins/zarin_mcp/__init__.py` — plugin, `_discover_handlers()` wiring.
- `plugins/zarin_mcp/registry.py` — `Registry`: `tool()` / `resource()` / `resource_template()` / `prompt()` decorators.
- `plugins/zarin_mcp/mcp_server.py` — `McpServer(host="127.0.0.1", port=9100)`: SSE app at `GET /sse` + `POST /messages/`, activity log (`recent_actions()`, max 300 entries).
- `plugins/zarin_mcp/handlers/` — `scenep/` (query + modify), `compp/` (transform + crud), `project.py`, `engine.py`, `assets.py`, `editor.py`, `console.py`, `resources.py`, `viewportp/` (camera + capture), `util.py`.
- `plugins/zarin_mcp/mcp_dock.py`, `main_thread.py` (`ensure_dispatcher()` / `run_on_main_thread()` — engine calls from worker threads are marshalled to the Qt main thread via a `QueuedConnection` dispatcher).
- `plugins/mcp_pack-2.1.1.zplugin` — packaged build of the bridge.
- `zarin_mcp_config.json` + `zarin_mcp_launcher.py` — client-side config and stdio↔SSE bridge (repo root).

## API

Handler areas (one verified tool signature shown; the rest follow the same `Registry.tool(name, description, inputSchema)` pattern):

- Scene query: `scene_list_entities(include_components=False, filter_name="", limit=50, offset=0)` — paginated, `filter_name` is a case-insensitive substring match.
- Scene modify (`scenep/modify.py`), component transform get/set (`compp/transform.py`), component add/remove/inspect (`compp/crud.py`), project, engine (play/stop/time), assets, editor selection, console, viewport camera + frame capture (`viewportp/`).

Resources (`handlers/resources.py`, 9 total):

| URI | Content |
|---|---|
| `scene://entities` | all entities with component data |
| `scene://hierarchy` | entity tree |
| `project://info` | root, loaded scene, entity count |
| `engine://status` | `play_mode`, `fps`, `frame_count`, `time_scale` |
| `editor://selection` | selected entities |
| `console://log` | last 100 log entries |
| `project://scenes` | `*.scene` files |
| `prefab://list` | `*.zpep` files |
| `asset://overview` | mesh/texture/audio/script counts |

Templates (2): `scene://entity/{entity_id}`, `scene://component/{component_type}`. Prompts (2): `analyze_scene(detail="basic")`, `project_overview()`. Counts match the client config description: `67 tools, 9 resources, 2 templates, 2 prompts`.

## Config + launcher

`zarin_mcp_config.json`:

```json
{
  "mcpServers": {
    "zarin-engine": {
      "command": "python",
      "args": ["zarin_mcp_launcher.py"],
      "cwd": ".",
      "description": "Zarin Engine — connects to open editor via SSE bridge (67 tools, 9 resources, 2 templates, 2 prompts). Start the editor first."
    }
  }
}
```

`zarin_mcp_launcher.py` bridges MCP stdio to `http://{ZARIN_MCP_HOST}:{ZARIN_MCP_PORT}/sse` (defaults `127.0.0.1:9100`), forwarding both directions with 3 connection attempts.

## Call example

```jsonc
// tools/call over the MCP session:
{ "name": "scene_list_entities",
  "arguments": { "filter_name": "Player", "limit": 50, "offset": 0 } }
// -> { "entities": [{ "id": "...", "name": "Player", "active": true, ... }], ... }
// resource read:
{ "uri": "engine://status" }
// -> { "play_mode": false, "fps": 60.0, "frame_count": 1234, "time_scale": 1.0, ... }
```

Start the editor first — the launcher only bridges; the SSE server runs inside the editor process (`_server.start_sse()` on `initialize`).

## Verified against

`plugins/zarin_mcp/__init__.py`, `plugins/zarin_mcp/registry.py`, `plugins/zarin_mcp/mcp_server.py`, `plugins/zarin_mcp/handlers/scenep/query.py`, `plugins/zarin_mcp/handlers/resources.py`, `plugins/zarin_mcp/main_thread.py`, `zarin_mcp_config.json`, `zarin_mcp_launcher.py`.
