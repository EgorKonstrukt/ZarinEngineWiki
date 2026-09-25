# Hot Reload

A script file can be edited while Play is running. The change is picked up on the next frame: the instance is recreated, Inspector values are preserved, and `on_awake` / `on_start` run again. Instance state from `__init__` is reset, like a recompilation in Unity.

Behavior details:

- The engine watches the file modification time. A newer `mtime` triggers reload before the next `on_update` / `on_fixed_update`.
- Field values edited in the Inspector are re-applied to the new instance, so tuning survives reload.
- A file with a syntax error is reported in the console as `Script '...' has N error(s):` with `file:line` entries. The previous working version keeps running until the file is fixed.
- The `Check` button in the Script Editor validates without starting Play: syntax plus `core.*` imports, including the hint to use `core.maths.math3d` instead of `core.math3d`.
- Hot reload is controlled per component by the `hot_reload` flag on `ScriptComponent` (default `True`). Set it to `False` to freeze the instance for the session.

Typical loop: press Play, tweak a speed or a curve response in the external editor, see the result in the running game on the next frame, keep tuned values in the Inspector, save the scene when satisfied.
