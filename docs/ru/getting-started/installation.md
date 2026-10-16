# Установка

> Установка ZarinEngine из исходников: установка Python-пакетов, запуск `python main.py`, однократная сборка нативных расширений.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

Инсталлятора нет. Движок работает из исходников: `main.py` — редактор, `player.py` — рантайм. Первый старт компилирует Cython-расширения inplace (`setup.py build_ext --inplace`); сплэш-скрин показывает прогресс по модулям.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| install_cmd | command | `pip install -r requirements.txt` | Установка всех Python-зависимостей |
| editor_cmd | command | `python main.py` | Старт редактора из корня движка |
| scene_arg | CLI arg | `main.py [file.zpes]` | Открыть `.zpes`-сцену при старте (или переслать запущенному инстансу через IPC); `.zterr`-графы террейна открываются в редакторе террейна |
| global_settings | path | `~/.zarin/settings.json` | Создаётся при первом запуске; хранит глобальный `Config` |
| ext_rebuild | automatic | on start if missing/stale | `main.py` сравнивает mtime `core/pyx/*.pyx` с собранными расширениями |

## Использование в редакторе

Неприменимо — эта страница приводит вас в редактор.

## Использование в коде

```bash
pip install -r requirements.txt
python main.py
```

```bash
# Открыть сцену сразу (повторный запуск пересылает её запущенному редактору):
python main.py scenes/SampleScene.zpes
```

## Сериализация

Неприменимо.

## Связанные страницы

- [Системные требования](./requirements.md)
- [Быстрый старт](./quick-start.md)
- [Обзор редактора](./editor-tour.md)
- [Конфигурация](../core/config.md)

## Проверено по

- `README.md` (раздел Quick start)
- `main.py` (аргумент `file`, `IpcServer`, `_check_extensions_missing`)
- `setup.py` (делегирует `core.build_cython.build`)
- `core/config/config.py` (`get_global_config`, путь `~/.zarin/settings.json`)
