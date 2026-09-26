# SvgRenderer

Crisp resolution-independent vector art in world space: icons, signage, map overlays, UI panels baked into the world.

| Field | Default | Meaning |
|---|---|---|
| svg_path | — | Vector source file |
| color | white | Tint multiplier |
| flip_x / flip_y | off / on | Mirroring (Y flip default compensates SVG coordinate origin) |
| pixels_per_unit | 100 | Rasterization density: texture pixels per world unit |

The SVG is rasterized to a texture at load through the internal `svgs` path — edges stay sharp at any import scale, unlike upscaled PNGs. Raise `pixels_per_unit` for close-up signage (200–400), lower it for distant icons (25–50) to save memory.

Workflows: wayfinding signage — one SVG per sign, shared component settings, per-sign path; map tables — large top-down SVG planes with camera-facing lights; icon walls — grids of small quads sharing a few vector files.

Vector art stays tiny on disk and scales infinitely at authoring time; the only runtime cost is the rasterized texture, sized by `pixels_per_unit` times world size. Keep world sizes modest or the texture balloons.

Troubleshooting: upside-down art → flip_y default is on for SVG origin reasons, toggle if your exporter differs; blurry at close range → raise pixels_per_unit and reimport; missing → path broken or unsupported SVG feature set, simplify gradients/filters.

Related: SpriteRenderer, TextRenderer, Materials (texture mapping), Cameras (signage framing).
