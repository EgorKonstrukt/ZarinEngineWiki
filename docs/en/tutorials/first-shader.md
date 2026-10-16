# Tutorial: First Shader

A custom material shader in 5 steps: `.shader` file (`Properties` + GLSL), `.zpem` material, assignment on `MeshRenderer`. Syntax from `core/assets/matp/shader.py` (`_SHADER_PROP_RE`, `_parse_shader_default`); material loading from `core/assets/matp/materials.py` (accepts `.mat` / `.zpem`, `load_shader_properties` requires the `.shader` extension); slots from `core/components/rendering/renderers/mesh_renderer.py` and `mesh_filter.py`.

Property line format: `[Attr] _Name("Display Name", Type) = default`. Types: `Color` / `Vector` → `(1, 1, 1, 1)`; `Float` / `Range(a, b)` → number; `Int` → integer; `2D` / `Cube` → `"white" {}`. Structure: `Shader "Zarin/MyShader" { Properties { … } SubShader { Tags { … } Pass { GLSLPROGRAM … // @FRAGMENT … ENDGLSL } } }` (see `core/shaders/materials/Unlit.shader`).

## Steps

1. **Create the shader.** Project panel → `Create → Shader`, save as `assets/shaders/pulse.shader`, paste the code below. Keep `GLSLPROGRAM` / `// @FRAGMENT` / `ENDGLSL` markers — the extractor splits vertex/fragment there.
2. **Create the material.** `Create → Material`, save as `assets/materials/pulse.zpem`, paste the JSON below (`shader_path` + `properties`; defaults fall back to the `Properties` block when omitted).
3. **Create the mesh holder.** Entity `Pulser` → `Add Component → MeshFilter` (`Source = <any .obj/.fbx>`, e.g. a cube; `Mesh = mesh`) → `Add Component → MeshRenderer`.
4. **Assign.** In `MeshRenderer → Materials → [0] → Material` pick `assets/materials/pulse.zpem`. The Inspector shows `_PulseSpeed` / `_PulseColor` from the shader's `Properties` block.
5. **Press Play.** The fragment pulses between albedo and `_PulseColor` at `_PulseSpeed` Hz. A `Shader path '…' not found, remapped` warning means the `shader_path` is wrong — it resolves against the project root, then the engine root / `core/shaders/`.

## Full code — `assets/shaders/pulse.shader`

```glsl
Shader "Zarin/Pulse"
{
    Properties
    {
        [MainColor] _BaseColor("Base Color", Color) = (1, 1, 1, 1)
        _PulseColor("Pulse Color", Color) = (1, 0.2, 0.4, 1)
        _PulseSpeed("Pulse Speed", Range(0, 8)) = 2.0
    }

    SubShader
    {
        Tags { "RenderType" = "Opaque" }

        Pass
        {
            GLSLPROGRAM
            #version 430 core
            layout(location = 0) in vec3 in_position;
            layout(location = 1) in vec3 in_normal;
            layout(location = 2) in vec2 in_uv;
            uniform mat4 u_model;
            uniform mat4 u_view;
            uniform mat4 u_proj;
            out vec2 v_uv;
            void main() {
                v_uv = in_uv;
                gl_Position = u_proj * u_view * u_model * vec4(in_position, 1.0);
            }

            // @FRAGMENT

            #version 330 core
            in vec2 v_uv;
            out vec4 frag_color;
            uniform vec4 _BaseColor;
            uniform vec4 _PulseColor;
            uniform float _PulseSpeed;
            uniform float u_time;
            void main() {
                float k = 0.5 + 0.5 * sin(6.28318 * _PulseSpeed * u_time);
                frag_color = mix(_BaseColor, _PulseColor, k);
            }
            ENDGLSL
        }
    }
}
```

## Full code — `assets/materials/pulse.zpem`

```json
{
  "name": "pulse",
  "shader_path": "assets/shaders/pulse.shader",
  "properties": {
    "_BaseColor": [1.0, 1.0, 1.0, 1.0],
    "_PulseColor": [1.0, 0.2, 0.4, 1.0],
    "_PulseSpeed": 2.0
  }
}
```

`MeshRenderer.materials` is `[{"path": "assets/materials/pulse.zpem"}]` (first entry = sub-mesh 0; `get_material_path(i)` falls back to the last entry). If the material stays flat white, the shader failed to compile — check the console log entry (`console://log` or the Console dock).

## Verified against

`core/assets/matp/shader.py`, `core/assets/matp/materials.py`, `core/shaders/materials/Unlit.shader`, `core/components/rendering/renderers/mesh_renderer.py`, `core/components/rendering/renderers/mesh_filter.py`.
