# Обзор ассетов

Всё в проекте — файл ассета + опциональный сайдкар `.import` JSON. Импортёры читают сайдкар, кэшируют по mtime/hash и отдают рантайм-буферы.

## Свойства / API

| Элемент | Источник | Смысл |
|---|---|---|
| Правило сайдкара | `texture_import_settings.py`, `importer/settings.py`, `audio/sys/clip.py` | `<asset>.import` JSON рядом с файлом |
| Меш | `importer/settings.py` | `scale` 1.0, `center_pivot` false, `flip_uvs` true, `smooth_angle` 30.0, `gen_normals/gen_uvs/blendshapes` true, `materials[]` |
| Текстура | `texture_import_settings.py` | `filter_mode` trilinear, `anisotropic` 1, `max_size` 2048, `wrap_mode` clamp, `compression` none, `srgb` true |
| Аудио | `audio/sys/clip.py` | `quality` 0–100 |
| Кэш | `importer/settings.py` | stat-сигнатура + LRU 12 / TTL 2.0 с |

## Использование в редакторе

1. Положите файл в `assets/`; рядом появится `.import` JSON.
2. Правьте ключи под тип (меш/текстура/аудио); реимпорт по смене mtime.
3. Нет сайдкара — дефолты выше; удаление сбрасывает.

## Использование в коде

```python
# меш .import
{"scale": 1.0, "center_pivot": false, "flip_uvs": true,
 "smooth_angle": 30.0, "gen_normals": true, "gen_uvs": true, "blendshapes": true}
# текстура .import
{"filter_mode": "trilinear", "anisotropic": 1, "max_size": 2048,
 "wrap_mode": "clamp", "compression": "none", "srgb": true}
# аудио .import
{"quality": 100}
```

## Связанные

- Импорт моделей, Текстуры, Настройки импорта

## Проверено по

- `core/assets/importer/settings.py`, `core/assets/texture_import_settings.py`, `core/audio/sys/clip.py`
