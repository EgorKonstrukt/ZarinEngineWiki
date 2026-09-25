# Gizmos and Icons

Editor-only overlay passes: gizmo lines/solids, billboard icons, selection outlines and the grid. They render after everything else in editor viewports and never appear in builds.

How components plug in:

- Each component declares `_gizmo_pass` (e.g. `"light"`, `"camera"`, `"audio"`, `"script"`) and optional `_icon`, `_gizmo_icon_color`, `_gizmo_icon_label`.
- Scripts can draw custom gizmos by defining `gizmo_lines()` / `gizmo_meshes()` — these run even outside Play.
- Lights show frustum/volume helpers, cameras show frustums, audio sources show zone shapes (sphere/box), colliders show wireframe shapes.

The icon pass batches billboarded labels (letter + color per component type), the outline pass highlights selection, and the grid pass draws the reference grid with overlay controls.
