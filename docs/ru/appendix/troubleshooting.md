# Решение проблем

> Исправления для сбоев, чья причина видна в коде. Только code-backed средства — без спекуляций.

[Home](../../index.md)

## Сессия Wayland ничего не рендерит / окно невидимо

`main.py:163-177`: когда `XDG_SESSION_TYPE` содержит `wayland`, а `QT_QPA_PLATFORM` не задан, редактор форсит `QT_QPA_PLATFORM=xcb` + `QT_XCB_GL_INTEGRATION=glx` ("Falling back to XCB for OpenGL compatibility"). Если явно задан `QT_QPA_PLATFORM=wayland`, печатается, что Qt6+OpenGL на Wayland "may cause window visibility issues". Фикс: снять `QT_QPA_PLATFORM` (дать фолбэку сработать) или выставить `QT_QPA_PLATFORM=xcb` вручную. Только то, что делает код, — без советов по композиторам.

## Нативные расширения отсутствуют или stale (`.pyx` новее `.pyd`)

`main.py:78-108` (`_check_extensions_missing`, скип при frozen): 25 отслеживаемых имён модулей под `core/` сверяются с mtime `core/pyx/*.pyx` — отсутствующий `.pyd`/свежий исходник возвращает True. Сплэш-билдер запускает `python setup.py build_ext --inplace` (`main.py:117-159`, прогресс по `total_exts = 25`). Фикс: запустить `python setup.py build_ext --inplace` вручную (добавить `--compiler=msvc` или `ZARIN_COMPILER=msvc` для форса MSVC вместо автозагружаемого MinGW-w64; см. `core/build_cython.py:18-63`). При неуспехе сплэш пишет "Extension build failed (exit code …)", старт продолжается без нативов.

## Тишина в аудио — "OpenAL not available"

`core/audio/sys/system.py:64-66`: когда импорт `openal` упал при загрузке модуля (`system.py:13-17`), init пишет варнинг "OpenAL not available, audio disabled" и работает без звука. `core/audio/efx/efx.py:15,197-203` и `filters.py:28`, `slots.py:78-82` бросают `EFXError("EFX not available")` / "Failed to create …" при отсутствии OpenAL/EFX. Фикс по коду: поставить OpenAL-рантайм + `pip install openal`; EFX-пути требуют рабочего EFX-контекста (`ensure_efx`). Не-WAV форматы дополнительно требуют `miniaudio` (`clip.py:164-167`: "miniaudio not available; cannot load …").

## Импорт моделей падает — "Assimp library not found"

`core/assets/importer/assimp.py:16-19,31-56`: загрузчик ищет `assimp-vc143-mt.dll` (win32, корень репо) или `libassimp.so.6.0.5` (прочие ОС), с фолбэком на голое имя библиотеки в `PATH`; оба бинарника лежат в корне репо. При неудаче `_get_dll()` бросает `RuntimeError("Assimp library not found")`. Фикс: вернуть shipped DLL/SO рядом с корнем проекта (путь = `_find_project_root()` подъёмом до родителя `core/`) или поставить Assimp в путь загрузчика.

## Падает сборка Nuitka

`tools/nuitka/build.py:build()` печатает `Python:`, `Root:`, резолвит сцены через `_get_included_scenes` (missing-сцена → "WARNING: scene not found", `config.py:116`), резолвит компилятор через `tools/mingwpm` ("WARNING: MinGW-w64 auto-download failed … Falling back to Nuitka's own toolchain download", `config.py:86-87`), вокруг — минификация PIL (`_minify_pil` оставляет Bmp/Gif/Jpeg/Ppm/Png, потом restore). Фикс по коду: повторить с `--compiler msvc` (тулчейн Visual Studio) или `--compiler mingw` (компактный MinGW-w64); передать `--no-strip-unused`, если скан сцен выкинул нужное; проверить пути `BuildSettings.json:scenes`.

## Чёрный вьюпорт (нет GL-контекста)

`main.py:206-209` и `player.py:268-271` запрашивают `QSurfaceFormat`: depth 24, OpenGL **4.6 CoreProfile**, swap interval из `rendering.vsync`. Чёрный вьюпорт без исключения = контекст не создался на стороне драйвера. Фикс по коду: убедиться в GL 4.6-драйвере (формат в коде не обсуждается); переключить `rendering.vsync` в `~/.zarin/settings.json` чтобы исключить swap-interval.

## Настройки побились — сброс к дефолтам

Глобальный файл — `~/.zarin/settings.json` (`core/config/config.py:142`); проектный — `<project>/ProjectSettings.json` (`config.py:321`). `Config.save()` пишет pretty JSON (`indent=2`); битый файл даёт "Failed to load config …" и фолбэк на вшитые дефолты (`config.py:93-101`). Фикс: удалить (или переименовать) `~/.zarin/settings.json` и перезапуститься — дефолты `get_global_config()` применятся заново; для проекта — аналогично удалить `ProjectSettings.json`. Restart-ключи (тема, шрифт/ui-масштаб, язык, `engine.python_*`) всё равно применяются только при следующем запуске.

## Файл сцены повреждён — ротация `.bak`

`core/engine/eng/scenes.py:20-52`: каждый сейв сцены ротирует бэкапы с `_SCENE_BACKUP_KEEP = 2` — прошлый `.bak` → `.bak2`, `.bak2` старше keep удаляется, текущий файл копируется в `.bak` (`shutil.copyfile`, атомарная запись через `.build`-temp, `scenes.py:55-59`). Фикс: восстановить `Scene.zpes` из `Scene.zpes.bak` (свежайшее) или `Scene.zpes.bak2` (на поколение старше).

## Проверено по

- `main.py:78-177, 206-213` (проверка/сборка расширений, фолбэк Wayland, GL 4.6 + vsync)
- `core/build_cython.py:18-83` (дефолт MinGW, оверрайд MSVC, флаги)
- `core/audio/sys/system.py:13-17, 64-100`, `core/audio/efx/efx.py`, `filters.py`, `slots.py`, `core/audio/sys/clip.py:160-167`
- `core/assets/importer/assimp.py:16-56` (имена DLL/SO, кандидаты, RuntimeError)
- `tools/nuitka/config.py:72-117`, `tools/nuitka/build.py` (минификация PIL, варнинги)
- `player.py:268-275`, `core/config/config.py:86-101, 142, 313-321`, `core/engine/eng/scenes.py:20-59`
