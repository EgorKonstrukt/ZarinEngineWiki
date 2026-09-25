# ScriptComponent

`ScriptComponent` is the engine side of user scripts: it loads the `.py` file, builds Inspector fields from the class, forwards lifecycle calls, and stores field values in the scene.

Component facts:

- `script_path` is stored relative to the project root, so scenes are portable. A relative path that does not resolve directly is retried against the project root.
- `hot_reload` defaults to `True`.
- Several `ScriptComponent` instances may live on one entity.
- The Inspector header shows the file display name (base name without extension) with a reveal action and an editor button.

Class resolution, in order:

1. If the file modification time is unchanged, the cached class is reused.
2. The script folder is added to the import search so sibling modules (including Cython builds) resolve.
3. The file is checked for errors. On errors a single consolidated console message is written per file version and the old instance is dropped; the component renders no fields until the file is fixed.
4. The module is executed with the injected API (`Input`, `KeyCode`, `Vec2/3/4`, `Curve`, `Range`, `Logger`).
5. The engine picks the first class defined in that file (alphabetical attribute order) that declares at least one lifecycle callback, collision callback, gizmo method, or `_inspector_buttons`. The class is instantiated with no arguments.
6. Per-method lookup caches bound methods and the `on_update` / `on_fixed_update` fast flags; collision arities are measured once via signature inspection.

The `Check` action runs the same validation as step 3 on demand and reports `file:line: message` rows, so broken scripts can be diagnosed without pressing Play.
