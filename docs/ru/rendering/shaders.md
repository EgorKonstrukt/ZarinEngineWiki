# Шейдеры (.shader)

Кастомный текстовый формат в стиле Unity ShaderLab. Минимальный пример:

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

Правила формата:

- Записи `Properties` выглядят как `[_Attr] _Name("Display", Type) = default`. Типы: `Color`/`Vector` как `(1,1,1,1)`, `Float`/`Range(a,b)` как float, `Int` как int, `2D`/`Cube` как `"white"` / `"bump"` плюс `{}`.
- В файле может быть несколько блоков `SubShader`; загрузчик пробует их по порядку, затем опциональный `Fallback "Name"`.
- Каждый блок `GLSLPROGRAM … ENDGLSL` делится на вершины/фрагменты по маркеру `// @FRAGMENT`.
- Загрузчик инжектит атрибуты GPU-инстансинга (`in_model0-3`, SSBO-биндинги 4/5 на 430+), UV-оверрайды (`u_uv_scale`, `u_uv_offset`, `u_uv_world_scale`) и маркеры инклюдов area-теней и каустики.

Встроенная библиотека в `core/shaders`: 9 шейдеров материалов (`PBR`, `Unlit`, `Sky`, `Skybox`, `Water`, `WaterSim`, `Tree`, `Clouds`, `CloudLayer`), 20+ внутренних (`Default`, `Shadow`, `Particle`, `ParticleGpu`, `Sprite`, `Text`, `Video`, `Icon`, `Gizmo`, `Grid`, `Outline`, `ObjectFx`, `Projector`, `GaussianSplat`, `Caustics`, `Underwater`, …) и общие сниппеты `include/`.
