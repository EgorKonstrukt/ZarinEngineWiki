# Materials (.zpem)

Material assets use `.zpem` (and legacy `.mat`) — both are JSON documents `{name, shader_path, properties}`. An empty `shader_path` falls back to the default shader. Properties missing from the file are completed from the shader `Properties` block at load, so hand-written files only carry overrides.

Texture uniform mapping (excerpt): `albedo_texture → u_albedo_tex`, `normal → u_normal_tex`, `roughness → u_roughness_tex`, plus direct `_BaseMap`, `_NormalMap`, `_OcclusionMap`. Value aliases: `_BaseColor → u_albedo_color`, `_Metallic → u_metallic`, `_Smoothness → u_smoothness`, `_EmissionColor → u_emission`.

Defaults applied at draw: albedo white `[1,1,1,1]`, metallic 0, smoothness 0.5, emission 0, a 1×1 white texture on unit 0, real textures from unit 1 with `*_Active` / `u_use_*` flags toggling each map.

Transparency rule: a surface is treated as transparent when its alpha drops below `0.999` or when the bound albedo texture contains an alpha channel (detected via image inspection) — no manual blend flag to forget.

Authoring workflow: create via the material wizard (picks shader, scaffolds defaults), preview in the material preview thumbnail, assign into MeshRenderer slots, tune live in the Inspector — runtime property edits apply immediately without reimport. Materials serialize by path, so renaming the file breaks slots until re-picked; prefer the Project panel rename action.

PBR quick guide (`PBR.shader` properties): `_BaseColor`/`_BaseMap` for albedo, `_Metallic` 0 dielectric / 1 metal, `_Smoothness` for gloss, `_NormalMap` for detail, `_OcclusionMap` for crevices, `_EmissionColor` × `_EmissionIntensity` for glow, `_Cutoff` for alpha-tested cutout, `_Transmission`/`_IOR` for glass-like transmission, `_double_sided` for foliage and cards.

Troubleshooting: black surface → shader failed and default has no light, check console; wrong colors → texture in sRGB vs linear mismatch or missing `u_use_*` flag; invisible transparent → alpha exactly 1 with alpha texture present, or depth-sorting against another transparent; changes lost → edited the preview copy, apply back to the asset.

Related: Shaders, MeshRenderer, Material Wizard and Previews (Editor Manual), Textures and Compression.
