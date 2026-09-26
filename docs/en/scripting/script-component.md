# ScriptComponent

`ScriptComponent` is the engine side of user scripts: it loads the `.py` file, builds Inspector fields from the class, forwards lifecycle calls, stores field values in the scene, and validates on demand.

Component facts:

- `script_path` is stored relative to the project root, so scenes are portable. A relative path that does not resolve directly is retried against the project root; absolute paths resolve as-is.
- `hot_reload` defaults to `True`.
- Several `ScriptComponent` instances may live on one entity (`_allow_multiple = True`).
- The Inspector header shows the file display name (base name without extension) with a reveal action and an editor button; a second header lists the discovered script variables.

Class resolution, in order:

1. Modification-time short-circuit: unchanged `mtime` reuses the cached class with zero work.
2. Import preparation: the script folder joins the module search so sibling `.py` and compiled Cython modules resolve.
3. Error collection: syntax parse, `core.*` import checks, trial execution. On errors one consolidated `Script '...' has N error(s):` message is logged per file version, the old instance is dropped, and no fields render until the file is fixed.
4. Module execution with the injected API (`Input`, `KeyCode`, `Vec2/3/4`, `Curve`, `Range`, `Logger`).
5. Class pick: the first class defined in that file (alphabetical attribute order) declaring at least one lifecycle callback, collision callback, gizmo method, or `_inspector_buttons`. Instantiated with no arguments; `self._entity` is assigned before every call.
6. Method caching: bound methods per canonical name with fast flags for `on_update` / `on_fixed_update`; collision arities measured once via signature inspection; field hints cached until the file changes.

Serialization: `script_path`, `hot_reload` and the `_field_values` dict persist in `.zpes`, so tuned instances survive reloads, prefabs and version control.

The `Check` action runs step 3 on demand and reports `file:line: message` rows — including the `core.maths.math3d` vs `core.math3d` hint and circular-import failures — so broken scripts are diagnosed without pressing Play.

Multiple instances on one entity each keep an independent module state, instance and field set; they reload independently. Cross-script communication goes through the entity (`get_component` by sibling script is resolved via its component, or shared state on the entity).
