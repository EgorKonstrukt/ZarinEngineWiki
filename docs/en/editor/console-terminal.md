# Console and Terminal

> Two log surfaces: `ConsolePanel` (`editor/panels/console/`) — the structured engine-log view with level filters; `TerminalPanel` (`editor/panels/terminal/`) — tabbed Python REPL + PowerShell consoles.

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `ConsolePanel` | `(parent=None)` + `load_config(config)` | `console/panel.py` dock |
| `ConsolePanel` pipeline | `_on_log_entry/_flush_pending/_add_entry/_should_show/_update_list` | Grouped, filtered list |
| `ConsolePanel` actions | `_clear/_collapse_all/_on_filter_changed/_rebuild/_update_counts/_render_detail/_deselect` | Toolbar + detail pane |
| `ConsoleGroup` | `(entry: LogEntry)` + `matches(entry)`, `display_text()` | `console/model.py` dedup group |
| `TerminalPanel` | `(parent=None)` + `load_config/save_config` | `terminal/panel.py` dock |
| `TerminalPanel._add_tab` / `_open_powershell` / `_close_tab` | `()` / `(index: int)` | Tab management |
| `TerminalPanel.execute_python` | `(code: str)` | Runs code in the active REPL namespace |
| `TerminalPanel._init_namespace` / `_reset_namespace` | `()` | REPL namespace lifecycle; `_on_scene_loaded` rebinds scene |
| `TerminalTab` | `(namespace: dict, font_family="", font_size=0, ...)` + `set_namespace(ns)`, `_execute(code)` | `terminal/repl.py` Python tab with history (`_history_up/_history_down`) |
| `PowershellTab` | `(assets_path: str, ...)` + `_send_command(cmd)`, `terminate()` | `terminal/powershell.py`; `PsReader` thread pumps stdout |

## Usage in Editor

Console: level filter + text filter narrow the list; entries collapse into `ConsoleGroup` rows; selecting a row renders the full detail (stack trace when present); Clear and Collapse-all reset the view. Terminal: `+` opens a Python REPL tab bound to a namespace containing engine/scene handles; the PowerShell button opens a shell rooted at the assets path. REPL `Up`/`Down` walks command history; output is color-split (stdout vs traceback).

## Usage in Code

```python
terminal.execute_python("print([e.name for e in scene.get_all_entities()])")
terminal._reset_namespace()
```

## Related

- Core Logging (LogEntry/LogLevel producers), Profiler (frame data), Status bar
- `editor/console_terminal.py` (legacy wiring shim — panels above are canonical)

## Verified against

- `editor/panels/console/panel.py`, `editor/panels/console/model.py`
- `editor/panels/terminal/panel.py`, `editor/panels/terminal/repl.py`, `editor/panels/terminal/powershell.py`
