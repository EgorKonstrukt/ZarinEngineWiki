# MCP Bridge

Model-Context-Protocol server exposing engine operations to AI agents: scene queries, object creation, property edits, screenshots, code execution — the same surface this documentation workflow drives.

Layout in `plugins/zarin_mcp/`:

- `mcp_server.py` — protocol server: tool registration, request dispatch, response framing.
- `registry.py` — tool catalog: every exposed operation registers with name, schema and handler.
- `handlers/` — one module per domain (scene, objects, viewport, execution), keeping the server file thin.
- `main_thread.py` — GUI-thread dispatch: document and view mutations must run on the main thread, so background requests marshal through here.
- `mcp_dock.py` — editor panel: server status, connected clients, request log, start/stop.

Repo roots: `zarin_mcp_config.json` and `zarin_mcp_launcher.py` at the project root configure and launch the bridge outside the editor; `plugins/mcp_pack-2.1.1.zplugin` ships it as a signed distributable (see Packaging).

Workflow: launch the bridge (dock or launcher) → connect an MCP-capable agent with the config → the agent lists tools from the registry → scene edits flow as validated operations with screenshots back. Every mutation is an explicit tool call — auditable in the dock log.

Safety posture: the bridge can rewrite scenes and run code — bind it to localhost, gate launches behind the dock switch, and review the request log like a sudo history.

Troubleshooting: agent cannot connect → server not started or config path wrong; tool missing → registry for that domain not loaded, check the dock; scene edits lag → main-thread queue saturated, lighten the request rate.

Related: .zplugin Packaging (the shippable pack), Plugin Manager (load tier), Gizmos (what agents see).
