# SvgRenderer

Crisp vector art in world space: icons, signage, map overlays.

| Field | Default | Meaning |
|---|---|---|
| svg_path | — | Vector source file |
| color | white | Tint multiplier |
| flip_x / flip_y | off / on | Mirroring |
| pixels_per_unit | 100 | Rasterization density |

The SVG is rasterized to a texture at load, so edges stay sharp at any import scale. Increase `pixels_per_unit` for close-up signage, lower it for distant icons.

Related: SpriteRenderer, TextRenderer, Materials (texture mapping).
