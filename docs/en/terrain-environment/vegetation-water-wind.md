# Vegetation, Water and Wind

Procedural `Tree`, boxed/infinite `WaterVolume` and `WindZone` samplers that drive shaders, physics and audio-adjacent motion.

## Properties / API

| Component.field | Default | Meaning |
|---|---|---|
| `Tree` (procedural trunk + cross-quad leaves) | — | Branch/leaf builder in `treep/component.py` + `treep/gen.py`; leaf color packs `(branch_level, stiffness, phase)` |
| `WaterVolume.mode` | Infinite | `Infinite` (whole level) or `Box` (`size` extents) |
| `WaterVolume.water_level / size / density` | 0.0 / 100³ / 1000.0 | Surface height, box size, kg/m³ |
| `WaterVolume.flow_strength / flow_direction` | 0.0 / 0.0 | Flow speed and compass deg |
| `WaterVolume.wave_amplitude / frequency / speed / choppiness` | 0.0 / 0.3 / 1.0 / 1.0 | Sine wave field; `height_at(x,z,t)`, `flow_at(x,y,z,t)`, `contains(x,y,z)` |
| `WindZone.mode` | Global | `Global` everywhere or `Local` inside `radius` with `falloff` |
| `WindZone.direction / speed` | 45.0 / 8.0 | Compass deg and m/s |
| `WindZone.turbulence / turbulence_scale` | 0.4 / 1.5 | Noise amplitude and scale |
| `WindZone.gust_strength / frequency / octaves / sharpness` | 0.5 / 0.25 / 4 / 0.3 | FBM gust envelope |
| `WindZone.vertical_boost / alignment / micro_turbulence / micro_frequency` | 0.2 / 0.7 / 0.15 / 8.0 | Lift, wave alignment, high-freq jitter |
| `WindZone.radius / falloff` | 100.0 / 0.5 | Local zone extent and edge softness |
| `sample(x,z,t) / sample_dir_vec3(x,z,t)` | — | Returns `dir/speed/turbulence/gust/strength` dict |

## Usage in Editor

1. Add `Tree` for procedural vegetation; `WaterVolume` for lakes/oceans (`Box` for ponds, `Infinite` for sea level).
2. Add `WindZone` (`Global` for weather, `Local` for courtyard gusts); tune direction/speed first, then turbulence/gusts.
3. Water `height_at` drives floating objects; wind `sample` drives cloth/particles/trees.

## Usage in Code

```python
water = entity.get_component_by_name("WaterVolume")
h = water.height_at(x, z, t)
flow = water.flow_at(x, y, z, t)

wind = entity.get_component_by_name("WindZone")
s = wind.sample(x, z, t)
print(s["dir"], s["speed"], s["gust"])
```

## Related

- Terrain, Sky/atmosphere, Physics buoyancy, Audio wind loops

## Verified against

- `core/components/environment/treep/component.py`, `treep/gen.py`
- `core/components/environment/water_volume.py`, `wind_zone.py`
