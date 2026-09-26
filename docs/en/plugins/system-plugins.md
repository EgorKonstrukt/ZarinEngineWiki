# System Plugins Catalog

Shipped in `plugins/` with `SYSTEM = True`: engine-grade extensions that load first and stay on. One page each for the verified two; capsule summaries for the rest (fields verified by filename, details in their panels).

| Plugin | File(s) | Role |
|---|---|---|
| Network Plugin | `network_plugin.py` | Gameplay transport wiring: host/connect, message handlers, per-frame poll. See Network Plugin page. |
| Physics Plugin | `physics_plugin.py` | Solver modes (single/multi/per-layer), background process, scene sync. See Solver and Threading. |
| Physics Drag Plugin | `physics_drag_plugin.py` | Editor mouse-dragging of rigidbodies in the viewport. |
| Physics Visualisation Plugin | `physics_visualisation_plugin.py` | Collision-shape and layer-color debug drawing. |
| Mesh Editor Plugin | `mesh_editor_plugin.py` | In-editor ProBuilder-style modeling panel and operations. |
| VR Plugin | `vr_plugin/` (`vr_core.py`, `vr_dock.py`) | VR runtime core plus editor dock. See VR Plugin page. |
| MCP Bridge | `zarin_mcp/` (server, dock, registry, handlers) | Model-Context-Protocol server exposing engine tools to AI agents. See MCP Bridge page. |
| Tracker Music Plugin | `tracker_music_plugin/` | Tracker music playback (nodmod engine) for retro scores. |
| Plotter Plugin | `plotter_plugin/` | Curve/data plotting panels. |
| QtQuick Plugin | `qt_quick_plugin/` | QtQuick surface hosting inside the editor. |

System plugins register components, docks and panels exactly like user plugins (same base class, same hooks) — the only differences are load tier and the engine's reliance on them. Disabling the physics or network plugin degrades the matching subsystem; the manager panel warns before you do.

`.pending/` holds staged plugins awaiting activation; `mcp_pack-2.1.1.zplugin` is the shippable MCP artifact (see Packaging).

Related: Plugin Manager (load tiers), Network Plugin, VR Plugin, MCP Bridge, Music/Plotter/QtQuick page.
