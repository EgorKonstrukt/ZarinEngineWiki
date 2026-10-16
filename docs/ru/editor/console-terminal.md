# Консоль и терминал

> Две поверхности логов: `ConsolePanel` (`editor/panels/console/`) — структурированный вид лога движка с фильтрами уровней; `TerminalPanel` (`editor/panels/terminal/`) — вкладки Python REPL + PowerShell.

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| `ConsolePanel` | `(parent=None)` + `load_config(config)` | Док `console/panel.py` |
| Конвейер `ConsolePanel` | `_on_log_entry/_flush_pending/_add_entry/_should_show/_update_list` | Группированный фильтруемый список |
| Действия `ConsolePanel` | `_clear/_collapse_all/_on_filter_changed/_rebuild/_update_counts/_render_detail/_deselect` | Тулбар + панель деталей |
| `ConsoleGroup` | `(entry: LogEntry)` + `matches(entry)`, `display_text()` | Группа-дедупликация в `console/model.py` |
| `TerminalPanel` | `(parent=None)` + `load_config/save_config` | Док `terminal/panel.py` |
| `TerminalPanel._add_tab` / `_open_powershell` / `_close_tab` | `()` / `(index: int)` | Управление вкладками |
| `TerminalPanel.execute_python` | `(code: str)` | Выполнение кода в неймспейсе активного REPL |
| `TerminalPanel._init_namespace` / `_reset_namespace` | `()` | Жизненный цикл неймспейса REPL; `_on_scene_loaded` перепривязывает сцену |
| `TerminalTab` | `(namespace: dict, font_family="", font_size=0, ...)` + `set_namespace(ns)`, `_execute(code)` | Вкладка Python в `terminal/repl.py` с историей (`_history_up/_history_down`) |
| `PowershellTab` | `(assets_path: str, ...)` + `_send_command(cmd)`, `terminate()` | `terminal/powershell.py`; поток `PsReader` качает stdout |

## Usage in Editor

Консоль: фильтр уровня + текстовый фильтр сужают список; записи схлопываются в строки `ConsoleGroup`; выбор строки показывает полные детали (со стектрейсом, если есть); Clear и Collapse-all сбрасывают вид. Терминал: `+` открывает вкладку Python REPL с неймспейсом, содержащим хендлы движка/сцены; кнопка PowerShell открывает шелл с корнем в папке ассетов. В REPL `Up`/`Down` ходят по истории; вывод разделён по цветам (stdout vs traceback).

## Usage in Code

```python
terminal.execute_python("print([e.name for e in scene.get_all_entities()])")
terminal._reset_namespace()
```

## Related

- Ядро логирования (производители LogEntry/LogLevel), Профайлер (кадровые данные), Статус-бар
- `editor/console_terminal.py` (легаси-прокладка — каноничны панели выше)

## Verified against

- `editor/panels/console/panel.py`, `editor/panels/console/model.py`
- `editor/panels/terminal/panel.py`, `editor/panels/terminal/repl.py`, `editor/panels/terminal/powershell.py`
