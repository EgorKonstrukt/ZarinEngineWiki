# Настройки импорта и кэш

Сайдкар `<file>.import` JSON переопределяет импорт; mtime/stat-сигнатуры решают, когда реимпортить.

## Свойства / API

| Ассет | Ключи сайдкара | Источник |
|---|---|---|
| Меш | `scale`, `center_pivot`, `flip_uvs`, `smooth_angle`, `gen_normals`, `gen_uvs`, `blendshapes`, `materials[]` | `importer/settings.py` |
| Текстура | `filter_mode`, `anisotropic`, `max_size`, `wrap_mode`, `compression`, `srgb` | `texture_import_settings.py` |
| Аудио | `quality` (0–100) | `audio/sys/clip.py` |

## Использование в редакторе

1. Создайте `<asset>.import` рядом с ассетом только с нужными ключами.
2. Троньте файл для реимпорта; удалите для сброса.
3. Аудио `quality: 100` — полный рейт; ниже — линейный ресемпл (мин. 8000 Гц).

## Использование в коде

```python
# меш
{"scale": 0.01, "flip_uvs": true, "smooth_angle": 30.0}
# текстура
{"filter_mode": "bilinear", "wrap_mode": "clamp", "max_size": 1024}
# аудио
{"quality": 80}
```

## Связанные

- Обзор ассетов, Импорт моделей, Текстуры, AudioClip

## Проверено по

- `core/assets/importer/settings.py`, `core/assets/texture_import_settings.py`, `core/audio/sys/clip.py`
