# Gizmos and Icons

Editor-only overlay passes: gizmo lines/solids, billboard icons, selection outlines and the grid. They render after everything else in editor viewports and never appear in builds — zero runtime cost, full staging value.

How components plug in:

- Each component declares `_gizmo_pass` (e.g. `"light"`, `"camera"`, `"audio"`, `"script"`, physics and navigation passes) routing its drawing into the right batch.
- Optional `_icon` file, `_gizmo_icon_color` and `_gizmo_icon_label` produce the billboarded letter badges (Light `L`, Audio `A`, Projector `P` and friends) through the icon pass.
- Scripts draw custom gizmos by defining `gizmo_lines()` / `gizmo_meshes()` — these execute even outside Play, ideal for patrol paths, spawn radii and sensor cones.
- Built-ins draw their domain shapes: lights show frustums/volumes, cameras show frustums, audio sources show zone shapes (sphere/box) with distance rings, colliders show wireframe shapes, joints show anchors and axes, navigation shows agent radii.

Passes behind the scenes: the icon pass batches billboards (one draw for many badges), the outline pass highlights the selection, the grid pass draws the reference grid with overlay controls, and the line width defaults to 0.6667 for crisp debug drawing.

Workflow: stage with gizmos on (light cones, audio zones, trigger boxes all visible), then toggle overlays off for beauty screenshots. Custom script gizmos should be cheap — they run every editor frame.

Troubleshooting: gizmo missing → component `_show_gizmo_icon` disabled or pass filtered in viewport options; script gizmo stale → hot-reload recreated the instance, re-open the method; clutter → hide passes per category in the viewport menu.

Related: Scripting gizmo callbacks, Light/Projector/AudioSource/Collider pages (their shapes), Viewport (Editor Manual).
