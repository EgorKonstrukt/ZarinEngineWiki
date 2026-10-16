# Начало работы

> Точка входа в ZarinEngine: игровой движок на Python с ECS-архитектурой и визуальным редактором. Установка зависимостей, запуск редактора через `python main.py`, запуск сцен через `python player.py`.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

ZarinEngine — 3D игровой и симуляционный движок на Python (ECS-архитектура, рендеринг ModernGL, редактор PySide6). API в духе Unity: иерархия сцены, компоненты, инспектор, префабы. Лицензия: Mozilla Public License 2.0.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| entry_editor | command | `python main.py` | Открывает визуальный редактор (см. `main.py`) |
| entry_player | command | `python player.py <scene.zpes>` | Запуск собранной игры из файла сцены (см. `player.py`) |
| entry_build | command | `python build_nuitka.py` | Компиляция standalone-исполняемого файла через Nuitka |
| scene_extension | str | `.zpes` | Файл сцены (JSON) |
| language | str | Python 3.13+ | Единственный нужный язык; компиляция для итераций не требуется |

## Использование в редакторе

1. Установите зависимости, запустите `python main.py`.
2. Прочитайте [Системные требования](./requirements.md), затем [Установка](./installation.md).
3. Пройдите [Быстрый старт](./quick-start.md), осмотрите интерфейс в [Обзор редактора](./editor-tour.md).
4. Соберите [Первая сцена](./first-scene.md). При сбоях откройте [Частые вопросы](./faq.md).

## Использование в коде

```python
# Подготовка проекта не требуется. Из корня движка:
#   pip install -r requirements.txt
#   python main.py                  # редактор
#   python player.py SampleScene.zpes  # запуск сцены
```

## Сериализация

Неприменимо. Сохранение сцен — в [Реестр и сериализация](../core/registry-serialization.md) и [Сцены](../scenes-prefabs/scenes.md).

## Связанные страницы

- [Системные требования](./requirements.md)
- [Установка](./installation.md)
- [Быстрый старт](./quick-start.md)
- [Обзор редактора](./editor-tour.md)
- [Первая сцена](./first-scene.md)
- [Частые вопросы](./faq.md)
- [Обзор ECS](../core/ecs-overview.md)
- [Скриптинг](../scripting/overview.md)

## Проверено по

- `README.md` (возможности, быстрый старт, требования, лицензия)
- `main.py` (точка входа редактора)
- `player.py` (точка входа плеера, `DEFAULT_SCENE`)
- `build_nuitka.py` (точка входа сборки)
- `LICENSE` (MPL-2.0)
