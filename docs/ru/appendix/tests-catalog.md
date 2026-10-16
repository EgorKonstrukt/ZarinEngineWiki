# Каталог тестов

> Каждый `tests/smoke/test_*.py` (16 файлов) — одной строкой, плюс раннеры. Все смоки — standalone-скрипты (`sys.path.insert` корня репо), каждый гоняется в свежем процессе с `QT_QPA_PLATFORM=offscreen` (`run_smokes.py:9-23`).

[Home](../../index.md)

## Смок-тесты (`tests/smoke/`)

| файл | что гоняет |
|---|---|
| `test_blendshapes.py` | Синтетический `MeshData` + деформ `BlendShapes` (импорты `core.renderer.meshd`, `blendshapes`) |
| `test_compression_async.py` | Асинхронное сжатие ресурсов сцены против бюджета 150 КБ уровня `fast` — **известное падение** (минимум 159 КБ, `ARCHITECTURE.md:52-53`) |
| `test_digest_dedup.py` | Раундтрип дайджест/дедупликации эмбеда (`core.ecs.embed`) во temp-директории |
| `test_duplicate_skinned.py` | Дублирование скинд-меша (`Transform`, `skinned_factory`, арматура) |
| `test_embed2.py` | `embed_scene_resources` / `extract_embedded_resources` с переносами файлов в `.bak` |
| `test_embed_outside.py` | Эмбед с ресурсами вне корня проекта (`_storage_key`, `_iter_component_path_entries`) |
| `test_engine_embed.py` | Полный флоу `Engine` + `MeshFilter` с переносами в `.bak` |
| `test_ik_numeric.py` | Числа `two_bone_flat` / `fabrik_flat` из `core._ik` |
| `test_import_materials.py` | Пайплайн импорта материалов ассетов + `MeshRenderer` |
| `test_mesh_import_persist.py` | Персистентность импорта `quad.obj` под temp-корнем `proj/assets/meshes` |
| `test_prop_picker.py` | Проп-пикер по `.anim`-клипу — **известное падение** (нет position-кривых, `y=0.1413` не фаерится, `ARCHITECTURE.md:54`) |
| `test_relpaths.py` | Относительные пути `quad.obj` + материалов под temp `proj/` |
| `test_script_cython.py` | Сборка Cython-компаньона юзер-скрипта (`cythonp`, `ScriptComponent`) во temp-директории |
| `test_script_inspector.py` | Виджеты инспектора скриптов в offscreen (`QApplication`, `QPushButton/QLineEdit/QSlider`) |
| `test_scripting.py` | Жизненный цикл скриптов: `Engine` + `Scene` + `ScriptComponent` |
| `test_soft_remote.py` | Headless-раундтрип софт-боди в отдельном физпроцессе |

## Раннеры

- `tests/smoke/run_smokes.py` — итерирует `SMOKE_DIR.glob("test_*.py")` по сортировке, `subprocess.run([PYTHON, script], cwd=ROOT, PYTHONPATH=ROOT, QT_QPA_PLATFORM=offscreen)`, печатает `== name` / `PASS` / `FAIL (exit N)`, итог `FAILED: …` или `ALL N SMOKE TESTS PASSED`, exit 1/0 (`run_smokes.py:19-38`). Запуск: `py tests/smoke/run_smokes.py`.
- `tools/arch_gate.py --smoke` — тот же сьют через субпроцесс `run_smokes()`, парсит строки `FAILED:`, терпит только `KNOWN_SMOKE_FAILS = {"test_compression_async.py", "test_prop_picker.py"}` (`arch_gate.py:309-318,340-346`).
- `tests/libs/test_library_packs.py` — сьют library-паков (zplug-паки; в шапке импорты `json/os/sys/tempfile`). Запуск: `python tests/libs/test_library_packs.py`.

## Как добавить свой тест

1. Создать `tests/smoke/test_<name>.py` как standalone-скрипт: вставить корень репо в `sys.path` (скопировать преамбулу `sys.path.insert(0, r"…ZarinEngine")`), Qt гардить через `os.environ.setdefault("QT_QPA_PLATFORM", "offscreen")` до `QApplication`.
2. Exit `0` при успехе, non-zero при провале — раннер смотрит только `returncode` (`run_smokes.py:27-33`).
3. Тесты, трогающие движок, делать hermetic: temp-корни `tempfile.mkdtemp(prefix=…)` как в `test_mesh_import_persist.py` / `test_relpaths.py`; фикстуры переносить в `*.bak` (не удалять), если нужен restore, по примеру `test_engine_embed.py:67-70`.
4. Новые файлы под `tests/` скипаются style-скопом гейта (`arch_gate.py:72`), но гоняются под `--smoke`; не расширять `KNOWN_SMOKE_FAILS` без правки `ARCHITECTURE.md:52-54`, если тест падает по предсуществующей причине.

## Проверено по

- Листинг `tests/smoke/` (16 `test_*.py` + `run_smokes.py`), шапка каждого тест-файла (импорты/первые строки), `tests/smoke/run_smokes.py` (env, цикл, exit-коды)
- `tools/arch_gate.py:38,309-346` (KNOWN_SMOKE_FAILS, парсинг смоков), `ARCHITECTURE.md:46-54` (команды гейта, 2 известных падения)
- `tests/libs/test_library_packs.py` (шапка), `tools/prop_picker_smoke.py` (родственный смок пикера)
