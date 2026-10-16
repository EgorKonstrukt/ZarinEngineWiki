# Shader Graph

> Node-based shader authoring (`editor/shader_graph/`): typed nodes in `nodesp/` assembled on a `NodeGraphQt` canvas, compiled to GLSL by `code_generator.generate_shader_code`.

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `generate_shader_code` | `(graph, shader_name: str = "Custom/GeneratedShader") -> str` | `shader_graph/code_generator.py` entry point |
| `_generate_vertex_section` / `_generate_fragment_section` | `(graph, nodes, uid_map, vertex_output/fragment_output) -> str` | Stage emitters |
| `_empty_shader` | `(name) -> str` | Fallback when the graph has no output path |
| Node base | `_ShaderNode(BaseNode)`; `_InputNode`, `_OutputNode`, `_MathNode`, `_TextureNode`, `_VectorNode` in `nodesp/base.py` | Every node implements `generate_code(uid_map)` |
| Input nodes | VertexPosition, UV, Normal, Time, Color, Float, Texture2D (`nodesp/inputs_outputs.py`) | Constants and mesh/shader inputs |
| Output nodes | VertexOutput, FragmentOutput (`nodesp/inputs_outputs.py`) | Graph sinks per stage |
| Math/vector nodes | Add, Multiply, Subtract, Lerp, Normalize, DotProduct, Fresnel, Step, Clamp (`nodesp/math_vector.py`) | GLSL expression emitters |

## Usage in Editor

The shader-graph dock presents the node palette (inputs, math, texture, outputs); links connect typed ports; the canvas validates that exactly one vertex path and one fragment path reach the output nodes; Apply regenerates the GLSL via `generate_shader_code` and assigns it to the edited shader asset. Empty or disconnected graphs fall back to `_empty_shader`, so the material never breaks.

## Usage in Code

```python
from editor.shader_graph.code_generator import generate_shader_code

glsl = generate_shader_code(graph, shader_name="Custom/MyEffect")
```

## Related

- Terrain Node Graph (same NodeGraphQt foundation, heightfield domain), Material Wizard and Previews
- Rendering: Shaders, Materials

## Verified against

- `editor/shader_graph/code_generator.py`, `editor/shader_graph/__init__.py`
- `editor/shader_graph/nodesp/base.py`, `editor/shader_graph/nodesp/inputs_outputs.py`, `editor/shader_graph/nodesp/math_vector.py`
- `editor/NodeGraphQt/`
