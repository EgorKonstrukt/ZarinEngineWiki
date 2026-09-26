# Hot Reload

A script file can be edited while Play is running. The change is picked up on the next frame: the instance is recreated, Inspector values are preserved, and `on_awake` / `on_start` run again. Instance state from `__init__` is reset, like a recompilation in Unity.

Reload pipeline:

1. The engine compares the file modification time before each `on_update` / `on_fixed_update`. A newer `mtime` starts a reload.
2. The file is re-validated (same checks as the Check button). On errors a single consolidated console message `Script '...' has N error(s):` is written per file version, and the previous working instance keeps running.
3. On success the module re-executes with the injected API, the behavior class is re-resolved, and a fresh instance is constructed.
4. Inspector field values are re-applied onto the new instance, then `on_awake` and `on_start` run again.

What survives: Inspector-tuned values, the component wiring, the entity hierarchy. What resets: anything assigned in `__init__` or built up in callbacks (timers, caches, spawned handles) — rebuild it in `on_awake`/`on_start` if the behavior must continue seamlessly.

The Check button validates without Play in three stages: syntax parse with `file:line` positions, `core.*` import resolution (including the hint to use `core.maths.math3d` instead of `core.math3d`), and a full trial execution catching runtime import errors such as circular imports.

Control: the `hot_reload` flag on `ScriptComponent` (default `True`). Set `False` to freeze the instance for the session — useful when debugging native-side state or comparing against a fixed baseline.

Workflow tips: keep Play running and iterate on feel (speeds, curves, timings) with an external editor; the tuned Inspector values persist across reloads, so save the scene when the feel is right. For structural refactors (renamed fields, new buttons) expect one clean reload cycle and re-check the Inspector.

Troubleshooting: changes ignored → the file did not save (mtime unchanged) or `hot_reload` is off; old behavior persists with errors → fix the reported `file:line` entries, the last working build stays live meanwhile; fields reset → the annotation was renamed, the old stored value has no target.
