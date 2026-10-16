# Terrain Graph and NodeGraphQt

> Procedural heightfield authoring (`editor/terrain_graph/` + `editor/panels/terrain/`): noise/mask/filter nodes evaluated on GPU, baked to heightmaps by `generate_shader`, previewed per node.

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `generate_shader` | `(graph, resolution: int) -> Tuple[str, dict, float]` | `terrain_graph/code_generator.py`; returns GLSL source, uniforms, bake time |
| `run_shader` / `run_preview_shader` | `(source: str, resolution: int, uniforms: Optional[Dict[str, float]] = None) -> Optional[np.ndarray]` | `terrain_graph/gpu_runner.py`; `_ensure_ctx()` guards context |
| `release` / `clear_cache` | `()` | `gpu_runner.py` teardown |
| `save_graph` / `load_graph` | `(graph, path: str) -> bool` | `terrain_graph/graph_serializer.py` |
| `compute_node_preview` | `(target_node, graph, resolution: int = 64) -> Optional[np.ndarray]` | `terrain_graph/node_preview.py` ancestor-subgraph bake |
| `update_all_previews` / `clear_preview_cache` | `(graph, resolution=64)` / `()` | Batch refresh / evict |
| `NodePreviewWidget` | `preview_widget.py`, extends `NodeBaseWidget` | In-node heightmap thumbnail |
| `TerrainGraphCollabBridge` | `QObject` in `terrain_graph/collab_bridge.py` | Forwards graph edits to the collab session |
| Generators | `PerlinNoiseNode`, `RidgedNoiseNode`, `BillowNoiseNode`, `VoronoiNode`, `ConstantNode`, `ContinentMaskNode`, `DuneNode` (`nodesp/generators.py`) | Height sources |
| Filters | `DomainWarpNode`, `ErosionNode`, `TerraceNode`, `PlateauNode`, `SlopeMaskNode`, `StrataNode`, `SharpenNode`, `SmoothNode`, `NormalizeNode`, `InvertNode`, `HeightBiasNode`, `RiverNode`, `ClampNode`, `RemapNode` (`nodesp/filters.py`) | Shaping passes |
| Combiner + sink | `AddNode`, `SubtractNode`, `MultiplyNode`, `BlendNode`, `MaxNode`, `MinNode`, `HeightOutputNode` (`nodesp/output.py`) | Mixers and the graph sink |
| Panel | `editor/panels/terrain/panel.py` + `graph_widget.py` | Dock embedding the canvas |

## Usage in Editor

Terrain dock: drop generator nodes (Perlin/Ridged/Voronoi...), shape them with filters (Erosion/Terrace/DomainWarp...), mix and route into `HeightOutputNode`; every node shows a live `NodePreviewWidget` thumbnail (64 px default) computed from its ancestor subgraph only. Bake runs `generate_shader` at the target resolution and writes the heightmap into the terrain component. Graph files save/load through the serializer; edits propagate to collaborators via the bridge.

## Usage in Code

```python
from editor.terrain_graph.code_generator import generate_shader
from editor.terrain_graph.gpu_runner import run_shader
from editor.terrain_graph.graph_serializer import save_graph, load_graph
from editor.terrain_graph.node_preview import compute_node_preview

source, uniforms, bake_time = generate_shader(graph, resolution=512)
height = run_shader(source, resolution=512, uniforms=uniforms)
save_graph(graph, "assets/terrain/island.zterrain")
thumb = compute_node_preview(node, graph, resolution=64)
```

## Related

- Shader Graph (sibling node system), Terrain component docs, Collaboration (graph bridge)
- GPU journal tool: `tools/gpuj/`

## Verified against

- `editor/terrain_graph/code_generator.py`, `editor/terrain_graph/gpu_runner.py`, `editor/terrain_graph/graph_serializer.py`, `editor/terrain_graph/node_preview.py`, `editor/terrain_graph/preview_widget.py`, `editor/terrain_graph/collab_bridge.py`, `editor/terrain_graph/glsl_chunks.py`
- `editor/terrain_graph/nodesp/base.py`, `editor/terrain_graph/nodesp/generators.py`, `editor/terrain_graph/nodesp/filters.py`, `editor/terrain_graph/nodesp/output.py`
- `editor/panels/terrain/panel.py`, `editor/panels/terrain/graph_widget.py`
