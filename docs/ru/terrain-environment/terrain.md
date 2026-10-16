# Террейн

GPU-генерация высот (compute-шейдер) + CPU-сборка меша с нормалями и UV.

## Свойства / API

| Элемент | Источник | Смысл |
|---|---|---|
| `TerrainSettings.data` | `core/terrain/terrain_generator.py` | Словарь ключей; `get/set/copy/to_dict/from_dict` |
| `resolution / octaves / noiseSeed` | `_DEFAULTS` | 512 / 9 / 1337.0; clamp 16–2048 кратно 16 |
| `baseFrequency / lacunarity / persistence / heightScale` | `_DEFAULTS` | 0.010 / 2.03 / 0.5 / 120.0 |
| `warpeness / warpFrequency / ridge / billow` | `_DEFAULTS` | 0.6 / 0.018 / 0.55 / 0.0 |
| `thermalErosion / hydraulicErosion / erosionIterations` | `_DEFAULTS` | 0.35 / 0.5 / 16.0 |
| `generate_heightfield(settings)` | `TerrainGenerator` | Compute `terrain_gen.compute` через `moderngl`, возврат `float32` |
| `generate_mesh(settings, size)` | `TerrainGenerator` | `vertices/normals/uvs/indices/heightfield/size/resolution` |

## Использование в редакторе

1. Настройте resolution, frequency, heightScale, эрозию в инструменте террейна.
2. Сгенерируйте; сущность хранит `size` (по умолч. 1000.0).
3. После смены seed/параметров — регенерация heightfield на GPU.

## Использование в коде

```python
from core.terrain.terrain_generator import TerrainSettings, get_generator

s = TerrainSettings()
s.set("resolution", 512)
s.set("heightScale", 120.0)
mesh = get_generator().generate_mesh(s, size=1000.0)
```

## Связанные

- Растительность, вода и ветер; импорт моделей; сетка NavAgent

## Проверено по

- `core/terrain/terrain_generator.py`
