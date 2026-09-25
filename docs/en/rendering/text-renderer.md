# TextRenderer

World-space text runs: labels, signage, debug overlays.

Rendered with font, size, color and alignment through the internal `Text` shader. Follows the entity Transform like any other renderer, so text panels rotate and scale with their mounts.

For screen-space UI text prefer GUI Label and related widgets; this component is for diegetic text inside the 3D world.

Related: SpriteRenderer, SvgRenderer, GUI Controls.
