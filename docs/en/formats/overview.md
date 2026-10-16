# File Formats Overview

All formats are plain text (JSON or a Unity-ShaderLab-like block format). Paths inside files are stored relative to the project root, so scenes move between machines.

| Extension | Type | Real shape |
|---|---|---|
| `.zpes` | Scene (JSON) | `{"entities": [{"id", "name", "active", "tags", "layer", "parent", "components", "prefab_guid"?}], ...}` — one entry per entity; each component dict carries its `type` plus a `_key` |
| `.zpep` | Prefab (JSON subtree) | Same entity-dict shape as `.zpes`, with stable GUIDs; instances add `prefab_guid` / `prefab_source_id` and per-instance overrides |
| `.zpem` / `.mat` | Material (JSON) | `{"name", "shader_path", "properties": {...}}` |
| `.shader` | Shader (block format) | `Shader "Name" { Properties { } SubShader { Tags { } Pass { GLSLPROGRAM ... } } }` with GLSL vertex/fragment inside |
| `.import` | Per-asset import settings (JSON) | Texture/mesh/audio sidecar: `{filter_mode, wrap_mode, ...}` next to the asset |
| `.zphysmat` | Physics material (resource pick) | Referenced by collider `physic_material` fields via the asset picker |
| `.animclip` / `.animcontroller` | Animation data (resource picks) | Referenced by animation components via the asset picker |

## `.zpes` entity block (from `Entity.serialize`)

```json
{
  "id": "7f3a...",
  "name": "Ball",
  "active": true,
  "tags": [],
  "layer": 0,
  "parent": null,
  "components": [
    {"type": "Rigidbody", "mass": 1.0, "use_gravity": true, "is_kinematic": false, "_key": "Rigidbody"},
    {"type": "ScriptComponent", "script_path": "assets/ball.py",
     "script_fields": {"push": 8.0, "hits": 0}, "_key": "ScriptComponent"}
  ]
}
```

Script fields serialize typed values losslessly: `Vec3` as `[x, y, z]`, `Enum` as its value, `Curve` as `{"keys": [{"time", "value", "in_tangent", "out_tangent", "tangent_mode"}], "pre_wrap", "post_wrap"}`. Entity references persist as UUID strings and resolve to live objects before each script call. The full `Rigidbody` dict additionally carries `drag`, `angular_drag`, `freeze_pos` / `freeze_rot` arrays alongside the abbreviated keys in the example above.

## Material (real file `assets/materials/ProBuilderPrototype.mat`)

```json
{"name": "ProBuilderPrototype", "properties": {"_BaseColor": [1.0, 1.0, 1.0, 1.0], "_EmissionColor": [0.0, 0.0, 0.0, 0.0], "_EmissionIntensity": 0.0, "_Metallic": 0.0, "_Smoothness": 0.5, "albedo_texture": "../textures/prototype_texture.png"}, "shader_path": "default"}
```

`Material.save` writes exactly `{"name", "shader_path", "properties"}` with texture paths relativized; `Material.load` accepts `.mat` and `.zpem` identically and falls back to `shader_path: "default"` when empty.

## Shader block (real file `core/shaders/internal/Default.shader`, head)

```shader
Shader "Zarin/Default"
{
    Properties
    {
    }

    SubShader
    {
        Tags { "RenderType" = "Opaque" }

        Pass
        {
            GLSLPROGRAM
            #version 430 core
            layout(location = 0) in vec3 in_position;
```

Materials declare runtime-editable properties (colors, floats, textures, vectors, ranges) that the `Properties { }` block exposes; mesh data reaches the GPU as `float32` converted at upload time from the engine's `float64` math.

## Import settings (real file `assets/textures/prototype_texture.png.import`)

```json
{"anisotropic": 4, "compression": "none", "filter_mode": "trilinear", "max_size": 2048, "srgb": true, "wrap_mode": "repeat"}
```

Mesh `.import` sidecars carry the same idea for geometry: scale, generate-normals/tangents flags, smoothing angle. The importer resolves `<asset>.import` next to the asset (including project-root-relative fallbacks) before falling back to defaults.

Related: Script Examples (field storage), ScriptComponent (how `script_fields` persist), API Index (loader entry points).
