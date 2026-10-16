# Горячие клавиши

> Полная карта шорткатов меню из `editor/main_window/menup/builder.py:31-190`. Клавиши камеры вьюпорта — только в объёме, показанном кодом (кей-хендлер, тултип-текст не найден).

[Home](../../index.md)

## Шорткаты меню

| шорткат | действие | меню |
|---|---|---|
| `Ctrl+N` | New Scene (`new_scene`) | File |
| `Ctrl+O` | Open Scene… (`open_scene`) | File |
| `Ctrl+S` | Save Scene (`save_scene`) | File |
| `Ctrl+Shift+S` | Save Scene As… (`save_scene_as`) | File |
| `Ctrl+Alt+S` | Save All Scenes (`_scene_tab_manager.save_all_dirty_tabs`) | File |
| `Alt+F4` | Exit (`mw.close`) | File |
| `Ctrl+Shift+N` | Create Empty (`_hierarchy._create_entity`) | GameObject |
| `Shift+F10` | Play/Stop (`toggle_play_stop`) | Game |
| `F10` | Launch in Player (`launch_external_player`) | Game |
| `Ctrl+P` | Go to Tab… (быстрое открытие; `ApplicationShortcut`) | View |
| `Ctrl+F` | Search Everywhere… (командная палитра; `ApplicationShortcut`) | View |
| `Ctrl+Tab` | Next Tab (`cycle_tab(1)`; `ApplicationShortcut`) | View |
| `Ctrl+Shift+Tab` | Previous Tab (`cycle_tab(-1)`; `ApplicationShortcut`) | View |
| `Ctrl+Shift+P` | Plugin Manager (`_plugin_mgr.show`) | Tools |
| `Ctrl+Shift+K` | Python Packages… (`_package_mgr.show`) | Tools |
| `Ctrl+Shift+M` | Mesh Editor (`_show_mesh_editor`) | Tools |
| `Ctrl+Shift+T` | Terrain Editor (`_show_terrain_editor`) | Tools |
| `Ctrl+Shift+G` | GUI Editor (`_gui_editor.show`) | Tools |
| `Ctrl+Shift+U` | Build Settings… (`_show_build_settings`) | Tools |
| `Ctrl+Shift+B` | Build Project… (`show_build_dialog`) | Tools |

Без шорткатов в коде: Undo/Redo (экшены есть, но в покое задизейблены, без `setShortcut`), Project Manager / Open Project, Add Entity…, Reset Layout, Refresh All Icons, Report Bug, About. Undo/redo стартуют выключенными (`_undo_act/_redo_act.setEnabled(False)`, `builder.py:68-75`).

## Вьюпорт (только по коду)

`editor/viewport/viewport_input.py:268-325`: `keyPressEvent` обрабатывает `Ctrl+C` (копия выделенного), `Ctrl+V` (вставка), `Ctrl+D` (дубликат = копия + вставка) по выделенным сущностям, затем скармливает клавиши в инпут-систему через маппинг `_qt_key_to_vk`: `W/A/S/D/Q/E/R/F`, `Shift/Ctrl/Alt`, `Delete`, `Space` (`viewport_input.py:322-325`). Мышь: `wheelEvent` → скролл/долли камеры (`viewport_input.py:261-266`); флаги гизмо-драга/орбиты, орбит навигационного гизмо, контекстное меню с Gizmo (Visible / Align To View-World-Local-Custom / Orientation Lock / Hide) + Create (`viewport_input.py:71,204,327-359`). Математика орбиты — в `navgizmo/gizmo.py:133-146,262-272`. Строк тултипов вьюпорта (`setToolTip` с текстом WASD/RMB/MMB) в `editor/viewport/` не найдено — таблица выше и есть полный честный объём; всё сверх (напр. "RMB = fly, MMB = pan") в коде не заявлено и опущено.

## Проверено по

- `editor/main_window/menup/builder.py:31-190` (каждый `setShortcut(QKeySequence(…))` выше; Undo/Redo без шорткатов)
- `editor/viewport/viewport_input.py:71-266, 268-325` (Ctrl+C/V/D, карта `_qt_key_to_vk`, wheel), `viewport_input.py:327-359` (контекстное меню), `editor/viewport/navgizmo/gizmo.py:133-272` (орбита)
- Честное отрицание: grep `setToolTip|RMB|MMB|WASD` в `editor/viewport/` даёт только орбит/нав-код, без хинт-тултипа управления
