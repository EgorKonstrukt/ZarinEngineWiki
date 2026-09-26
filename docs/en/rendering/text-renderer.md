# TextRenderer

World-space text runs: floating labels, signage, scoreboards, debug overlays readable inside the 3D view.

Rendered with font, size, color and alignment through the internal `Text` shader. Follows the entity Transform like any renderer, so text panels rotate and scale with their mounts — a scoreboard tilts with its pole, a nameplate tracks its NPC.

Workflows: NPC nameplates — small quad above the head with high-contrast color, billboarded by facing the camera each frame from a script; level signage — large static text with an unlit-style material for readability in any light; debug overlays — gizmo-adjacent readouts (positions, states) toggled by a debug flag.

Keep strings short and sizes generous: world-space text competes with texture detail, and tiny glyphs alias. For paragraphs and rich text use GUI Label/TextEdit widgets on screen space instead.

For screen-space UI text prefer GUI Label and related widgets; this component is for diegetic text inside the 3D world.

Troubleshooting: unreadable at angle → face the camera or raise size; washed out → text color fights the key light, switch to bright-on-dark; missing glyphs → font coverage, swap the font asset.

Related: SpriteRenderer, SvgRenderer, GUI Controls (Label, TextEdit), Cameras.
