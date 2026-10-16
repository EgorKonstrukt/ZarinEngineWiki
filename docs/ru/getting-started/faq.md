# Частые вопросы

> Короткие ответы на повторяющиеся вопросы установки и рантайма. Каждый ответ ссылается на код, задающий поведение.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

**В: Как запустить редактор?**
О: `pip install -r requirements.txt`, затем `python main.py` из корня движка (`README.md`, `main.py`).

**В: Как запустить сцену без редактора?**
О: `python player.py <scene.zpes>`. Порядок выбора: CLI-аргумент → первая запись `BuildSettings.json` `scenes` → `ProjectSettings.json` `project.default_scene` → `scenes/SampleScene.zpes` (`player.py:_resolve_startup_scene`).

**В: Какая версия Python?**
О: 3.13+ (`README.md`). Cython-расширения пересобираются автоматически при старте редактора, если устарели.

**В: Мой скрипт ничего не делает. Почему?**
О: Скрипты работают только в Play-режиме. Движок использует первый класс в файле, у которого есть lifecycle-колбэк (`on_update`, `on_start`, …); короткие алиасы (`update`, `start`) тоже работают. Исключения в колбэках логируются, игру не роняют (`docs/scripting.md`, `core/ecs/query.py`).

**В: Правки скрипта во время Play?**
О: Hot-reload пересоздаёт инстанс на следующем кадре, сохраняет значения инспектора, заново вызывает `on_awake`/`on_start`. Отключается флагом `hot_reload = False` на `ScriptComponent` (`docs/scripting.md`).

**В: Где хранятся настройки?**
О: Глобальные: `~/.zarin/settings.json`. Проектные: `<project>/ProjectSettings.json` (`core/config/config.py`).

**В: Пустое окно редактора на Wayland?**
О: `main.py` форсит `QT_QPA_PLATFORM=xcb` при детекте Wayland-сессии, ради совместимости OpenGL.

**В: Как собрать игру?**
О: `python build_nuitka.py` (конфигурация в `BuildSettings.json`; `README.md`, `build_nuitka.py` → `tools.nuitka.build`).

## Свойства

| name | type | default | описание |
|---|---|---|---|
| support_channel | file | `editor/bug_report.py` | `main.py`/`player.py` ставят bug-report хуки при старте |

## Использование в редакторе

Неприменимо.

## Использование в коде

Неприменимо.

## Сериализация

Неприменимо.

## Связанные страницы

- [Системные требования](./requirements.md)
- [Установка](./installation.md)
- [Быстрый старт](./quick-start.md)
- [Конфигурация](../core/config.md)
- [Логирование](../core/logging.md)

## Проверено по

- `README.md`, `main.py`, `player.py`, `build_nuitka.py`
- `docs/scripting.md` (контракт, hot-reload, кнопка `Check`)
- `core/config/config.py` (пути настроек)
- `core/ecs/query.py` (изоляция ошибок колбэков)
