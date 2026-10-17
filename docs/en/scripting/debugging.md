# Debugging Scripts

Where errors go, how to check a script before Play, and what the runtime hides from you.

## The Check button (Script Editor)

Toolbar button **Check** (`Run → Check Script` menu duplicates it):

| Location | Behavior |
|---|---|
| `editor/panels/script_editor_panel/wid/widget.py:196-197` | `QAction("Check")`, tooltip `"Check script for errors"` → `_check_current()`. |
| `editor/panels/script_editor_panel/wid/tabs_host.py:135-157` | `_check_current()`: reads the live diagnostics of the current tab and shows a message box: `No errors or warnings found.` on clean; `error [line N]: message` (first 15) + `warning [line N]: message` (first 15) otherwise. Status label shows `✖ E ⚠ W` / `⚠ W` / `✓ clean`. |

This is editor-side static analysis. The runtime adds a second net below.

## Runtime error net (`ScriptComponent`)

`core/components/scripting/scriptc/hotreload.py:99-149` — `_collect_script_errors(path)` returns `file:line:` strings:

| Format | Meaning |
|---|---|
| `path:0: cannot read file: ...` | File unreadable. |
| `path:lineno:offset: SyntaxError: msg` | `ast.parse` failed. |
| `path:lineno: No module named 'core.X' (did you mean 'core.maths.math3d' ...)` | Bad `core.*` import; note the hint — the math module is `core.maths.math3d`, not `core.math3d`. |
| `path:lineno: cannot import name 'Y' from 'core.X'` | Wrong symbol from a valid module. |
| `path:lineno: ExcType: msg` | Top-level execution error (traceback line extracted). |

With errors present, the class is **not** loaded (`_py_instance = None`, fields cleared) and the message is logged once per file revision via `Logger.error`.

Callback exceptions (`_call_py`, `hotreload.py:253-260`):

```python
try:
    meth(*args)
except Exception as e:
    Logger.error(f"Script {canonical} error '{self.script_path}': {e}")
```

Only `str(e)` is logged — **no traceback, no line number**. When a Play-mode error names the method but not the line, reproduce it under the Check button or wrap the suspect block yourself (see below). Hot-reload failures additionally keep the **previous working version** (`hotreload.py:292-301`): failed reload restores old class/instance/methods and retries on the next mtime change.

## Logger levels

`core/foundation/logger.py` — `LogLevel`: `DEBUG = 0`, `INFO = 1`, `WARNING = 2`, `ERROR = 3`:

| Call | Behavior |
|---|---|
| `Logger.debug(msg)` / `info` / `warning` | Emitted when `_min_level` allows; stored + broadcast to listeners (the editor console panel is one). |
| `Logger.error(msg, exc=None)` | Includes a formatted traceback **only** when `exc` is passed. |
| `Logger.get_entries()` / `Logger.clear()` / `add_listener(fn)` | History inspection and subscription. |

`Logger` is injected into every behavior script automatically.

## Diagnostics example (full, runnable)

A script that makes runtime failures locatable despite the `str(e)`-only logging:

```python
import traceback


class Diagnosable:
    verbose: bool = True

    def on_start(self):
        Logger.info(f"Diagnosable on {self._entity.name} (id={self._entity.id})")
        self.body = self._entity.get_component_by_name("Rigidbody")
        if self.body is None and self.verbose:
            Logger.warning("Diagnosable: no Rigidbody; physics branch disabled")

    def on_update(self, dt):
        try:
            self._tick(dt)
        except Exception as e:
            # full traceback with file:line, since _call_py logs only str(e):
            Logger.error(f"Diagnosable._tick failed: {traceback.format_exc()}")

    def _tick(self, dt):
        if Input.GetKeyDown(KeyCode.G):
            raise RuntimeError("repro case: press-G handler")
```

Checklist when a script silently does nothing: entity `active`? component `enabled`? `script_path` resolves (Inspector shows `Missing:` otherwise)? class detected (first `dir()`-order class with a lifecycle/collision/gizmo member)? axis defined (else `GetAxis` is `0.0`)? `play()` called with empty `clip_path`?

## Related

- `hot-reload.md` — reload keeps field values and re-runs `on_awake`/`on_start`.
- `script-component.md` — paths, flags, class resolution, Check.
- `pitfalls.md` — the full gotcha list with fixes.

## Verified against

- `editor/panels/script_editor_panel/wid/widget.py:196-197`, `wid/tabs_host.py:135-157` (Check button + `error [line N]` report).
- `core/components/scripting/scriptc/hotreload.py:99-149` (`file:line` formats), `:151-213` (inspect path, `dir(mod)` class scan), `:253-260` (`str(e)`-only logging), `:262-301` (mtime check, old-version fallback).
- `core/foundation/logger.py:13-17` (levels), `:49-68` (`debug/info/warning/error/get_entries/clear`).
