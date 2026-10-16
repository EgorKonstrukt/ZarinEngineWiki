# Системные требования

> Что нужно ZarinEngine для работы: Python 3.13+, PySide6, ModernGL, NumPy, OpenAL, Assimp и OpenGL 4.6.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

Движок — чистый Python плюс нативные библиотеки для окон, рендеринга, аудио и импорта мешей. C++-тулчейн для обычной работы не нужен (Cython-расширения собираются автоматически при первом старте редактора через `setup.py build_ext --inplace`).

## Свойства

| name | type | default | описание |
|---|---|---|---|
| python | version | `3.13+` | Версия интерпретатора (`README.md`) |
| PySide6 | package | `>= 6.6.0` | UI редактора, окна вьюпорта |
| ModernGL | package | `>= 5.10.0` | Бэкенд рендеринга OpenGL |
| NumPy | package | `>= 1.26.0` | Вся математика — `numpy.float64`; `float32` только при загрузке в GPU |
| OpenAL | system library | — | 3D позиционное аудио (`pyopenal`, `miniaudio`) |
| Assimp | bundled binary | — | `assimp-vc143-mt.dll` / `libassimp.so.6.0.5` для импорта мешей |
| OpenGL | driver feature | `4.6 Core Profile` | Запрашивается в `main.py` через `QSurfaceFormat` (буфер глубины 24) |
| full list | file | `requirements.txt` | `PySide6, moderngl, numpy, pyopenal, miniaudio, pynput, msgpack, websockets, cython, setuptools, nuitka, pillow, imageio, qtawesome, xxhash, psutil, culverin, pyyaml` |

## Использование в редакторе

Неприменимо. Требования проверяются до старта редактора: если нативные расширения отсутствуют или устарели, `main.py` пересобирает их с прогресс-баром на сплэш-скрине.

## Использование в коде

```python
# From the engine root:
#   pip install -r requirements.txt
#   python main.py
```

## Сериализация

Неприменимо.

## Связанные страницы

- [Установка](./installation.md)
- [Быстрый старт](./quick-start.md)
- [Частые вопросы](./faq.md)
- [Конфигурация](../core/config.md)

## Проверено по

- `README.md` (раздел Requirements)
- `requirements.txt` (полный список пакетов)
- `main.py` (`QSurfaceFormat` 4.6 Core, `_check_extensions_missing`, `_build_extensions_with_splash`)
- `core/maths/math3d.py` (`FLOAT_TYPE = np.float64`)
