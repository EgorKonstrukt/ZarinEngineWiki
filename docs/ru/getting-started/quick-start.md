# Быстрый старт

> Откройте редактор, нажмите Play, повесьте скрипт, запустите сцену в плеере. Пять минут от нуля до вращающегося объекта.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

Цикл: редактор (`main.py`) для авторства, Play-режим для проверки, `player.py` для запуска сцены без редактора. Скрипты — обычные `.py`-файлы; поведение сущности не требует базового класса.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| editor | command | `python main.py` | Среда авторства |
| play | editor button | Play | `Engine.start_play()` → `Scene.start()` → покадровый `tick()` |
| player | command | `python player.py <scene.zpes>` | Автономный запуск; выбор сцены: CLI-аргумент → `BuildSettings.json` scenes[0] → `ProjectSettings.json` `project.default_scene` → `scenes/SampleScene.zpes` |
| build | command | `python build_nuitka.py` | Standalone-исполняемый файл (настройки в `BuildSettings.json`) |

## Использование в редакторе

1. `python main.py`.
2. Создайте сущность в Hierarchy (контекстное меню Hierarchy само добавляет `Transform`; голый `Scene.create_entity()` в коде — нет).
3. `Project → Create → Python Script`, прикрепите через `Inspector → Add Component → Scripts`.
4. Нажмите Play. Файл скрипта можно править во время Play: инстанс пересоздаётся на следующем кадре, значения инспектора сохраняются (`hot_reload`).

## Использование в коде

```python
from core.maths.math3d import Vec3


class Rotator:
    speed: float = 90.0

    def on_update(self, dt: float):
        t = self._entity.get_component_by_name("Transform")
        if t:
            t.rotate(Vec3(0.0, self.speed * dt, 0.0))
```

```python
# Headless equivalent of pressing Play on the current scene:
engine.start_play()   # Engine.start_play(); Engine.stop_play() to exit
```

## Сериализация

Значения полей скрипта хранятся в сцене. Пример записи компонента:

```json
{ "type": "ScriptComponent", "enabled": true, "script_path": "assets/move.py",
  "script_fields": { "speed": 90.0 } }
```

## Скриншоты / Медиа

![Главное окно редактора ZarinEngine](../../assets/screenshots/editor-main.png)

> Что вы увидите после `python main.py`: главное окно редактора.
> Файл: `assets/screenshots/editor-main.png` (проверенная копия
> `zarin_screenshot.png` из репозитория движка). Видеозаписи: `.gif`-файлов
> в репозитории движка пока нет — как записать см.
> `assets/gifs/README.md` (ScreenToGif/Peek/LICEcap, ширина 800 px, < 5 МБ);
> готовые GIF класть в `assets/gifs/`.

## Связанные страницы

- [Первая сцена](./first-scene.md)
- [Обзор редактора](./editor-tour.md)
- [Сущность Entity](../core/entity.md)
- [Трансформ](../core/transform.md)
- [Скриптинг](../scripting/overview.md)
- [Плеер и сборка Nuitka](../build/player-nuitka.md)

## Проверено по

- `README.md` (Quick start, пример Scripting)
- `player.py` (`_resolve_startup_scene`, `_deduce_project_root`, `DEFAULT_SCENE = "SampleScene.zpes"`)
- `core/engine/eng/engine.py` (`start_play`, `stop_play`)
- `docs/scripting.md` (контракт скрипта, hot-reload)
- `core/components/scripting/scriptc/component.py` (`script_path`, `script_fields`)
