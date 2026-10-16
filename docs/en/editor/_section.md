# Editor Manual

The ZarinEngine editor, dock by dock — all facts read from `editor/` sources.

- Main Window — `EditorMainWindow` shell, menus, toolbar, verified hotkeys.
- Viewport, Camera, Gizmos — `SceneViewport` mixins, fly/orbit controls, grid snap.
- Hierarchy — entity tree, DnD, prefab ops, collab hooks.
- Inspector — components, pickers, copy/paste values, animator sub-editors.
- Project Panel and Assets — browser views, file ops with undo, creation wizards.
- Console and Terminal — structured log view, Python REPL + PowerShell tabs.
- Profiler Panel and Undo History — frame views, `UndoStack`, tracemalloc reports.
- Curve, Gradient, Color Editors — dialogs, presets, sampling.
- Material Wizard and Previews — live GL material/model thumbnails.
- Shader Graph — node → GLSL code generation.
- Terrain Graph and NodeGraphQt — GPU heightfield baking + per-node previews.
- Build Dialog, Project Manager, Settings — shipping dialogs.
- Animation, Scripts, Prefab, Mesh, VCS Panels — satellite docks catalog.
- Collaboration and MCP Docks — live session panel + AI-agent bridge.
