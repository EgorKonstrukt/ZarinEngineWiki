# System Plugins Catalog

Shipped in `plugins/` (verified `NAME` / `VERSION` / `SYSTEM` against each `get_plugin()` file; note: the shipped folder mixes `SYSTEM = True` engine tier and `SYSTEM = False` bundled tools — load tier is per-plugin, not per-folder). One page each for the verified two; capsule summaries for the rest.

| Plugin (`NAME`) | Version | `SYSTEM` | File(s) (`get_plugin()` present) | Role |
|---|---|---|---|---|
| NetworkPlugin | 1.0.0 | True | `network_plugin.py` | Gameplay transport wiring: host/connect, message handlers, per-frame poll. See Network Plugin page. |
| PhysicsPlugin | 0.4.0 | True | `physics_plug/plugin.py` (+ `physics_plug/__init__.py` re-export) | Solver modes (single/multi/per-layer), background process, scene sync. See Solver and Threading. |
| PhysicsVisualisation | 1.1.0 | True | `physviz/plugin.py` | Collision-shape and layer-color debug drawing. |
| QtQuickWorldPlugin | 1.0.0 | True | `qt_quick_plugin/plug/plugin.py` | QtQuick surface hosting inside the editor. |
| VRPlugin | 1.0.0 | True | `vr_plugin/plug/plugin.py` + `vr_plugin/vr_dock.py` + `vr_plugin/components/` | VR runtime core plus editor dock. See VR Plugin page. |
| ZarinMCP | 2.1.0 | False | `zarin_mcp/__init__.py` (server, dock, registry, handlers) | Model-Context-Protocol server exposing engine tools to AI agents. See MCP Bridge page. |
| PlotterPlugin | 1.2.0 | False | `plotter_plugin/__init__.py` | Curve/data plotting panels. |
| TrackerMusicPlugin | 1.0.0 | False | `tracker_music_plugin/__init__.py` | Tracker music playback (rend engine) for retro scores. |
| ExamplePlugin | 1.0.0 | False | `example_plugin.py` (43 lines) | Canonical minimal plugin. See walkthrough. |
| MeshEditorPlugin | 1.0.0 | False | `mesh_editor_plugin.py` | In-editor ProBuilder-style modeling panel and operations. |
| PhysicsDragPlugin | 0.1.0 | False | `physics_drag_plugin.py` | Editor mouse-dragging of rigidbodies in the viewport. |
| BSPImport | 2.0.0 | False | `user/bsp_import/plugin.py` | User-tier BSP import example. |

System plugins register components, docks and panels exactly like user plugins (same base class, same hooks) — the only differences are load tier and the engine's reliance on them. Disabling the physics or network plugin degrades the matching subsystem; the manager panel warns before you do.

`.pending/` holds staged plugins awaiting activation; `mcp_pack-2.1.1.zplugin` is the shippable MCP artifact (see Packaging).

Related: Plugin Manager (load tiers), Network Plugin, VR Plugin, MCP Bridge, Music/Plotter/QtQuick page.
