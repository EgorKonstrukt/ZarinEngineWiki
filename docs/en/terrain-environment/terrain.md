# Terrain

GPU heightfield generation (compute shader) plus CPU mesh build with normals and UVs.

## Properties / API

| Item | Source | Meaning |
|---|---|---|
| `TerrainSettings.data` | `core/terrain/terrain_generator.py` | Dict of keys below; `get/set/copy/to_dict/from_dict` |
| `resolution / octaves / noiseSeed / flipY` | `terrain_generator.py` (`_DEFAULTS`) | 512 / 9 / 1337.0 / 0; resolution clamped 16–2048, multiple of 16 |
| `baseFrequency / lacunarity / persistence / heightScale` | `_DEFAULTS` | 0.010 / 2.03 / 0.5 / 120.0 |
| `warpeness / warpFrequency / warpIterations` | `_DEFAULTS` | 0.6 / 0.018 / 3.0 |
| `ridge / ridgePower / ridgeSharpness / billow` | `_DEFAULTS` | 0.55 / 4.0 / 1.0 / 0.0 |
| `continentMask / continentScale / continentFalloff` | `_DEFAULTS` | 0.6 / 0.0025 / 1.4 |
| `thermalErosion / hydraulicErosion / erosionIterations / talus` | `_DEFAULTS` | 0.35 / 0.5 / 16.0 / 0.04 |
| `terrace / terraceSteps / dune / sharpen / heightBias` | `_DEFAULTS` | 0.0 / 8.0 / 0.0 / 0.0 / 0.0 |
| `generate_heightfield(settings)` | `TerrainGenerator` | Runs `terrain_gen.compute` via `moderngl` compute shader, returns `float32 res x res` |
| `generate_mesh(settings, size)` / `mesh_from_heightfield(hf, size)` | `TerrainGenerator` | Builds `vertices/normals/uvs/indices/heightfield/size/resolution` |

## Usage in Editor

1. Tune terrain settings (resolution, frequency, heightScale, erosion) in the terrain tool.
2. Generate; mesh entity stores `size` (default 1000.0) and resolution.
3. Re-generate after seed/parameter changes; heightfield is recomputed on GPU.

## Usage in Code

```python
from core.terrain.terrain_generator import TerrainSettings, get_generator

s = TerrainSettings()
s.set("resolution", 512)
s.set("heightScale", 120.0)
s.set("noiseSeed", 1337.0)
mesh = get_generator().generate_mesh(s, size=1000.0)
print(mesh["resolution"], len(mesh["vertices"]))
```

## Related

- Vegetation, Water and Wind; Models via Assimp; NavAgent grid

## Verified against

- `core/terrain/terrain_generator.py`, `core/shaders/compute/terrain_gen.compute`
