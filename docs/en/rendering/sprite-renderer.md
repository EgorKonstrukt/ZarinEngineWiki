# SpriteRenderer

World-space textured quad: billboards, pickups, decals, 2D characters inside 3D scenes, sprite-based VFX sheets.

| Field | Default | Meaning |
|---|---|---|
| texture_path | — | Sprite image |
| color | white `[1,1,1,1]` | Tint multiplier — white keeps source colors |
| flip_x / flip_y | off / off | Horizontal / vertical mirroring |

Drawn through a dedicated sprite path with instancing support, so hundreds of pickups batch cheaply. Follows the entity Transform for position, rotation and scale; billboard-facing behavior comes from the internal `Sprite` shader path.

Workflows: pickups — quad with texture, small emissive-leaning material, slow `Rotator` script for attention; foliage cards — crossed quads with alpha-tested materials and `_double_sided`; flip-book animation — swap `texture_path` on a timer from a script (or use the GIF flipbook importer upstream); decals — quads floated 1 cm off surfaces with transparent materials.

Tint tricks: white keeps art intact; red tint for damage flash (animate back over 0.2 s); per-instance color variation across a crowd from spawner scripts.

For full screen-space UI layouts use the GUI system (Image widget on GuiCanvas); SpriteRenderer is for diegetic content inside the world.

Troubleshooting: pink quad → texture path broken, re-pick; mirrored art → flip flags; sorting artifacts between sprites → transparent bucket sorts by depth, separate layers in Z; blurry pixels → texture filter import setting, use nearest for pixel art.

Related: SvgRenderer, TextRenderer, VideoRenderer, GUI Overview, Textures and Compression.
