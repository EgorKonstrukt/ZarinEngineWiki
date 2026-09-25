# SpriteRenderer

World-space textured quad: billboards, pickups, decals, 2D characters in 3D scenes.

| Field | Default | Meaning |
|---|---|---|
| texture_path | — | Sprite image |
| color | white | Tint multiplier `[1,1,1,1]` |
| flip_x / flip_y | off / off | Mirroring |

Drawn through a dedicated sprite path with instancing support; follows the entity Transform. For full UI layouts use the GUI system instead.

Related: SvgRenderer, TextRenderer, VideoRenderer, GUI Overview.
