# Shaders (.shader)

Custom text format in Unity ShaderLab style: a `Properties` header the editor understands plus raw GLSL the GPU compiles. Minimal example:

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

Format rules, verified from the loader:

- `Properties` entries look like `[_Attr] _Name("Display", Type) = default`. Types: `Color`/`Vector` as `(1,1,1,1)`, `Float`/`Range(a,b)` as float, `Int` as int, `2D`/`Cube` as `"white"` / `"bump"` plus `{}`.
- One file may hold several `SubShader` blocks; the loader tries them in order, then an optional `Fallback "Name"` shader when all fail.
- Each `GLSLPROGRAM … ENDGLSL` block splits into vertex/fragment at the `// @FRAGMENT` marker.
- The loader injects GPU-instancing attributes (`in_model0-3`, SSBO bindings 4/5 on 430+), UV overrides (`u_uv_scale`, `u_uv_offset`, `u_uv_world_scale`), and resolves include markers for area shadows and caustics.
- Shader lookup spans the engine root `core/shaders` tree: `materials`, `internal`, `compute`, `include`, `legacy`.

Authoring workflow: duplicate `Unlit.shader`, rename the `Shader "..."` path, declare inputs in Properties (they auto-appear in every material using it), write the passes, assign to a material and iterate with hot material preview. Compile errors print with file and line — fix top-down, the first error usually cascades.

Compilation fallback: `#version 430 core` is tried first; on failure the loader retries as `#version 330 core` with instancing and skinning defines forced off. Effects that fundamentally need 430 (SSBO paths) degrade visibly — check the console for the fallback notice.

Built-in library under `core/shaders`: 9 material shaders (`PBR`, `Unlit`, `Sky`, `Skybox`, `Water`, `WaterSim`, `Tree`, `Clouds`, `CloudLayer`), 20+ internal shaders (`Default`, `Shadow`, `Particle`, `ParticleGpu`, `Sprite`, `Text`, `Video`, `Icon`, `Gizmo`, `Grid`, `Outline`, `ObjectFx`, `Projector`, `GaussianSplat`, `Caustics`, `Underwater` and more) and shared `include/` snippets (`area_shadows.glsl`, `caustics.glsl`).

For node-based authoring see Shader Graph in the Editor Manual; for per-material values see Materials.

Related: Materials, MeshRenderer (UV override uniforms), Lights (light uniforms), Shader Graph.
