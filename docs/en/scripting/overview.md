# Scripting Overview

Entity behaviors in ZarinEngine are plain `.py` files with a behavior class. No base class, no registration boilerplate, no build step — the file itself is the asset.

End-to-end flow:

1. `Project → Create → Python Script` creates the file inside the project folder. Keep scripts next to the content that uses them; sibling imports (including Cython modules) resolve from the script folder automatically.
2. Select an entity, `Inspector → Add Component → Scripts`, pick the file in the `Script` field. Annotated class attributes instantly become Inspector widgets.
3. Tune values in the Inspector — they are stored in the scene (`.zpes`), not in the `.py` file, so one script drives many tuned instances.
4. Press `Play`. The engine loads the class, instantiates it with no arguments, assigns `self._entity`, and calls `on_awake` then `on_start`.

How it works inside:

- The engine finds the first class defined in the file that declares at least one lifecycle callback, collision callback, gizmo method, or `_inspector_buttons`. Inheritance is not required; the constructor must take no arguments.
- Short names are full aliases: `update` = `on_update`, `start` = `on_start`, and so on for awake, fixed_update, destroy, enable, disable. If both variants are defined, the `on_*` version wins.
- One entity can hold several `ScriptComponent` instances (`_allow_multiple = True`): movement, health and visuals as separate scripts on one object.
- An exception inside any callback never crashes the game: it is written to the console with the script and method name plus a traceback, and the frame continues with the next component.
- `script_path` is stored relative to the project root, so scenes and scripts move between machines cleanly.

Execution context:

- `on_update(dt)` runs in scene order every rendered frame with `dt` already multiplied by the time scale; `on_fixed_update(dt)` runs on the physics clock with a stable step (`0.02` default).
- Collision callbacks arrive from the physics dispatch, not the ECS loop, with the other entity id and optionally the impact force.
- Gizmo methods (`gizmo_lines`, `gizmo_meshes`) execute in the editor even outside Play for custom handles and debug drawing.

Performance guidance: scripts are Python and run per frame — keep `on_update` lean, cache component lookups in `on_start` instead of fetching by name every frame, and move number-crunching into Cython modules (see Cython in Scripts).

Subpages:

- Lifecycle Callbacks — every method, signature and call order.
- Inspector Fields and Range — annotations to widgets, Entity pickers, buttons.
- Input and Math API — injected names, keys, axes, vectors, curves.
- Hot Reload — editing scripts during Play.
- Cython in Scripts — compiled helpers with zero manual builds.
- ScriptComponent — paths, flags, class resolution, Check.
- Script Examples — copy-paste behaviors.
