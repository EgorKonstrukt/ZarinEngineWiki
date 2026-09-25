# Shaders (.shader)

Custom text format, Unity ShaderLab style. Minimal example:

```shader
Shader "Zarin/Unlit" {
  Properties {
    _BaseColor("Base Color", Color) = (1, 1, 1, 1)
    _BaseMap("Base Map", 2D) = "white" {}
  }
  SubShader {
    Tags { "RenderType" = "Opaque" }
    Pass {
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
      #version 430 core
      uniform vec4 u_albedo_color;
      in vec2 v_uv;
      out vec4 fragColor;
      void main() {
        fragColor = u_albedo_color;
      }
      ENDGLSL
    }
  }
}
```

Format rules:

- `Properties` entries look like `[_Attr] _Name("Display", Type) = default`. Types: `Color`/`Vector` as `(1,1,1,1)`, `Float`/`Range(a,b)` as float, `Int` as int, `2D`/`Cube` as `"white"` / `"bump"` plus `{}`.
- One file may hold several `SubShader` blocks; the loader tries them in order, then an optional `Fallback "Name"` shader.
- Each `GLSLPROGRAM … ENDGLSL` block splits into vertex/fragment at the `// @FRAGMENT` marker.
- The loader injects GPU-instancing attributes (`in_model0-3`, SSBO bindings 4/5 on 430+), UV overrides (`u_uv_scale`, `u_uv_offset`, `u_uv_world_scale`), and include markers for area shadows and caustics.

Built-in library under `core/shaders`: 9 material shaders (`PBR`, `Unlit`, `Sky`, `Skybox`, `Water`, `WaterSim`, `Tree`, `Clouds`, `CloudLayer`), 20+ internal shaders (`Default`, `Shadow`, `Particle`, `ParticleGpu`, `Sprite`, `Text`, `Video`, `Icon`, `Gizmo`, `Grid`, `Outline`, `ObjectFx`, `Projector`, `GaussianSplat`, `Caustics`, `Underwater`, …) and shared `include/` snippets.
