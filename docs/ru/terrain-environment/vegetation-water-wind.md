# Деревья, вода, ветер

Процедурный `Tree`, `WaterVolume` (Box/Infinite) и сэмплеры `WindZone` для шейдеров и физики.

## Свойства / API

| Компонент.поле | Умолчание | Смысл |
|---|---|---|
| `Tree` | — | Процедурные ствол + кросс-квады листьев (`treep/`) |
| `WaterVolume.mode` | Infinite | `Infinite` или `Box` с `size` |
| `WaterVolume.water_level / size / density` | 0.0 / 100³ / 1000.0 | Высота, размер бокса, плотность |
| `WaterVolume.flow_strength / flow_direction` | 0.0 / 0.0 | Течение и градусы |
| `WaterVolume.wave_*` | 0.0 / 0.3 / 1.0 / 1.0 | Амплитуда/частота/скорость/chop; `height_at`, `flow_at`, `contains` |
| `WindZone.mode` | Global | `Global` везде или `Local` в `radius` с `falloff` |
| `WindZone.direction / speed` | 45.0 / 8.0 | Градусы и м/с |
| `WindZone.turbulence / gust_* / micro_*` | 0.4 / 0.5 / 0.15 | Шум, порывы FBM, микроджиттер |
| `WindZone.radius / falloff` | 100.0 / 0.5 | Экстент локальной зоны |
| `sample(x,z,t)` | — | Словарь `dir/speed/turbulence/gust/strength` |

## Использование в редакторе

1. `Tree` для растительности; `WaterVolume` для озёр/морей (`Box` — пруд, `Infinite` — уровень моря).
2. `WindZone` (`Global` — погода, `Local` — порывы во дворе); сначала direction/speed, затем turbulence/gusts.
3. `height_at` — плавучесть; `sample` — ткань/частицы/деревья.

## Использование в коде

```python
water = entity.get_component_by_name("WaterVolume")
h = water.height_at(x, z, t)
wind = entity.get_component_by_name("WindZone")
s = wind.sample(x, z, t)
```

## Связанные

- Террейн, физика плавучести

## Проверено по

- `core/components/environment/treep/component.py`, `water_volume.py`, `wind_zone.py`
