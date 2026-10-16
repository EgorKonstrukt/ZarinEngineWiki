# MCP-инструменты (ZarinMCP)

Мост для LLM: MCP-сервер внутри редактора, отдающий сцену, компоненты, ассеты и захват вьюпорта как tools, resources, templates и prompts.

| NAME | VERSION | SYSTEM |
|---|---|---|
| `ZarinMCP` | `2.1.0` | `False` |

Объявлен в `plugins/zarin_mcp/__init__.py` (`ZarinMCPPlugin`). Регистрирует док `"ZarinMCP"` (область `right`), пункт меню `ZarinMCP → Open MCP Panel` и кнопку тулбара `MCP`.

## Файлы

- `plugins/zarin_mcp/__init__.py` — плагин, `_discover_handlers()`.
- `plugins/zarin_mcp/registry.py` — `Registry`: декораторы `tool()` / `resource()` / `resource_template()` / `prompt()`.
- `plugins/zarin_mcp/mcp_server.py` — `McpServer(host="127.0.0.1", port=9100)`: SSE-приложение `GET /sse` + `POST /messages/`, журнал активности (`recent_actions()`, максимум 300 записей).
- `plugins/zarin_mcp/handlers/` — `scenep/` (query + modify), `compp/` (transform + crud), `project.py`, `engine.py`, `assets.py`, `editor.py`, `console.py`, `resources.py`, `viewportp/` (camera + capture), `util.py`.
- `plugins/zarin_mcp/mcp_dock.py`, `main_thread.py` (`ensure_dispatcher()` / `run_on_main_thread()` — вызовы движка из воркеров маршалятся в главный поток Qt через диспетчер `QueuedConnection`).
- `plugins/mcp_pack-2.1.1.zplugin` — упакованная сборка моста.
- `zarin_mcp_config.json` + `zarin_mcp_launcher.py` — клиентский конфиг и stdio↔SSE-мост (корень репозитория).

## API

Области хендлеров (одна сигнатура показана полностью, остальные — тот же паттерн `Registry.tool(name, description, inputSchema)`):

- Запросы сцены: `scene_list_entities(include_components=False, filter_name="", limit=50, offset=0)` — постранично, `filter_name` — регистронезависимая подстрока.
- Изменение сцены (`scenep/modify.py`), get/set трансформа (`compp/transform.py`), добавление/удаление/инспекция компонентов (`compp/crud.py`), проект, движок (play/stop/time), ассеты, выделение редактора, консоль, камера вьюпорта + захват кадра (`viewportp/`).

Ресурсы (`handlers/resources.py`, всего 9):

| URI | Содержимое |
|---|---|
| `scene://entities` | все сущности с данными компонентов |
| `scene://hierarchy` | дерево сущностей |
| `project://info` | корень, загруженная сцена, число сущностей |
| `engine://status` | `play_mode`, `fps`, `frame_count`, `time_scale` |
| `editor://selection` | выбранные сущности |
| `console://log` | последние 100 записей лога |
| `project://scenes` | файлы `*.scene` |
| `prefab://list` | файлы `*.zpep` |
| `asset://overview` | счётчики мешей/текстур/аудио/скриптов |

Шаблоны (2): `scene://entity/{entity_id}`, `scene://component/{component_type}`. Промпты (2): `analyze_scene(detail="basic")`, `project_overview()`. Счётчики совпадают с описанием в клиентском конфиге: `67 tools, 9 resources, 2 templates, 2 prompts`.

## Конфиг + лаунчер

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

`zarin_mcp_launcher.py` соединяет MCP-stdio с `http://{ZARIN_MCP_HOST}:{ZARIN_MCP_PORT}/sse` (дефолт `127.0.0.1:9100`), проброс в обе стороны, 3 попытки соединения.

## Пример вызова

```jsonc
// tools/call в рамках MCP-сессии:
{ "name": "scene_list_entities",
  "arguments": { "filter_name": "Player", "limit": 50, "offset": 0 } }
// -> { "entities": [{ "id": "...", "name": "Player", "active": true, ... }], ... }
// чтение ресурса:
{ "uri": "engine://status" }
// -> { "play_mode": false, "fps": 60.0, "frame_count": 1234, "time_scale": 1.0, ... }
```

Сначала запустите редактор — лаунчер только мост; SSE-сервер живёт в процессе редактора (`_server.start_sse()` в `initialize`).

## Проверено по коду

`plugins/zarin_mcp/__init__.py`, `plugins/zarin_mcp/registry.py`, `plugins/zarin_mcp/mcp_server.py`, `plugins/zarin_mcp/handlers/scenep/query.py`, `plugins/zarin_mcp/handlers/resources.py`, `plugins/zarin_mcp/main_thread.py`, `zarin_mcp_config.json`, `zarin_mcp_launcher.py`.
