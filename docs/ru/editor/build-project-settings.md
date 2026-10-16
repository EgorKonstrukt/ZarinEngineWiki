# Диалог сборки, менеджер проектов, настройки

> Поверхность поставки: `BuildDialog` (разовая сборка Nuitka), `BuildSettingsDialog` (постоянный выбор сцен/плагинов/strip), диалог `ProjectManager`, диалоги Global/Project settings (`editor/settings/`).

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| `BuildDialog` | `(parent=None)`, заголовок «Build Zarin Engine» | `editor/build_dialog.py` |
| Цели сборки | `_target_combo` (player/editor), `_compiler_combo`, `_physics_combo` | Зеркалит выборы `tools/nuitka/config.py` |
| Флаги | `_onefile_cb` («Single file (--onefile)»), `_console_cb` («Show console window»), `_strip_cb` («Strip unused assets...»), `_winrt_cb` («Include Windows Runtime DLLs...») | Собираются в `_collect_args()` |
| Прогон | `_start_build()`, `_run_build(args)`, `_on_build_done(returncode)`, `_cancel_build()`, `_browse_output_dir()`, `_open_output_folder()`, `_append_log(text)` | Асинхронная сборка с логом |
| `BuildSettingsDialog` | `(parent=None)`, заголовок «Build Settings» | `editor/build_settings_dialog.py`; Save/Cancel сохраняют |
| Группы настроек | Список «Scenes In Build», чеклист «Plugins In Build», группа «Build Options» | Сцены берутся из `<project>/scenes`, плагины — из `<project>/plugins` |
| `_strip_check` | «Strip unused assets (scan scenes for references)» | Постоянный тоггл strip |
| `ProjectManager` | `editor/project_manager.py` | Диалог создания/открытия проекта |
| Настройки | `editor/settings/` | Диалоги Global Settings... / Project Settings... (меню Edit) |

## Usage in Editor

Меню File/Edit открывают Project Manager, Global/Project Settings. Флоу сборки: «Build Settings...» отмечает сцены и плагины в поставку и тоггл strip (хранится на проект); «Build...» открывает `BuildDialog` — цель (player `player.py` vs редактор `main.py`), компилятор (auto/mingw/msvc), физсолвер (culverin/pybullet/physx), флаги onefile/console/strip/WinRT, выходной каталог — затем сборка с живым логом и открытием папки результата при успехе.

## Usage in Code

```python
dlg = BuildDialog(parent=mw)
dlg._start_build()   # собирает _collect_args() и запускает сборку
```

```python
s = BuildSettingsDialog(project_root, parent=mw)
# Save сохраняет scenes-in-build, plugins-in-build, флаг strip
```

## Related

- Плеер и сборка Nuitka (CLI за диалогом), Инструменты и профилирование, Панель менеджера плагинов (кнопки build/package)

## Verified against

- `editor/build_dialog.py`, `editor/build_settings_dialog.py`, `editor/project_manager.py`, `editor/settings/`
- `tools/nuitka/config.py` (паритет флагов), `_build_BuildSettings.json` (образец настроек в корне репо)
