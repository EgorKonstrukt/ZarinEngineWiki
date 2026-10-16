# Material Library

> Every shipped material asset under `assets/materials/` (currently a single `.mat` file), the `.mat` JSON field table from `core/assets/matp/materials.py`, and a verbatim example.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Material assets are `.mat` JSON files (`Material { name, shader_path, properties }`), loaded by `Material` (`core/assets/matp/materials.py:14-169`) and cached by `MaterialManager` (`core/renderer/mats/manager.py:54-80`). `shader_path = "default"` maps to the built-in PBR; any other path is resolved against the project root first, then engine `core/shaders/` (`materials.py:26-46`). Missing files warn once and fall back to default (`manager.py:63-71`). No `.zpem` files ship under `assets/` at time of writing.

## Listing (`assets/materials/`)

| file | material | shader | purpose |
|---|---|---|---|
| `ProBuilderPrototype.mat` | `ProBuilderPrototype` | `default` (built-in PBR) | Grey prototype grid texture for blockout geometry (`albedo_texture: ../textures/prototype_texture.png`). |

## `.mat` JSON fields

| field | type | default | description |
|---|---|---|---|
| `name` | string | `""` | Material name (`materials.py:14-24`). |
| `shader_path` | string | `"default"` | `.shader` path or `"default"` for built-in PBR (`materials.py:48-52,110-118`). Non-`.shader` extensions are rejected when loading shader properties. |
| `properties` | object | `{}` | Shader uniforms (`manager.py:89-96`). Missing keys are back-filled from the shader's `Properties` defaults (`materials.py:63-66`). |

Shader resolution order for a non-default path (`materials.py:26-46`): absolute path if it exists → `<project_root>/<path>` → `<engine_root>/<path>` → `<engine_root>/core/shaders/<path>` → basename lookup inside `materials/`, `internal/`, `compute/`, `include/`, `legacy/`.

## Example (verbatim)

`assets/materials/ProBuilderPrototype.mat`:

```json
{"name": "ProBuilderPrototype", "properties": {"_BaseColor": [1.0, 1.0, 1.0, 1.0], "_EmissionColor": [0.0, 0.0, 0.0, 0.0], "_EmissionIntensity": 0.0, "_Metallic": 0.0, "_Smoothness": 0.5, "albedo_texture": "../textures/prototype_texture.png"}, "shader_path": "default"}
```

## Usage in Editor

Create a `.mat` file in Assets, pick a Shader, edit the exposed `Properties`. Assign the file to [Mesh Renderer](../mesh-renderer.md) material slots.

## Usage in Code

```python
from core.assets.matp import Material
m = Material.load("assets/materials/ProBuilderPrototype.mat", project_root)
m.set_property("_Metallic", 0.2)
m.save("assets/materials/ProBuilderPrototype.mat", project_root)
```

## Related pages

- [Materials](../materials.md), [Shaders](../shaders.md), [Shader Catalog](./shader-catalog.md), [Mesh Renderer](../mesh-renderer.md)

## Verified against

- `assets/materials/ProBuilderPrototype.mat` (verbatim above; sole `.mat` under `assets/`; no `.zpem` files under `assets/`)
- `core/assets/matp/materials.py:14-70` (`Material`, `_resolve_shader_path`, `load_shader_properties`, `save`)
- `core/renderer/mats/manager.py:54-100`
