# Materials (.zpem)

Material assets use `.zpem` (and legacy `.mat`) — both are JSON documents `{name, shader_path, properties}`. An empty `shader_path` falls back to the default shader. Properties missing from the file are completed from the shader `Properties` block at load.

Texture uniform mapping (excerpt): `albedo_texture → u_albedo_tex`, `normal → u_normal_tex`, `roughness → u_roughness_tex`, plus direct `_BaseMap`, `_NormalMap`, `_OcclusionMap`. Value aliases: `_BaseColor → u_albedo_color`, `_Metallic → u_metallic`, `_Smoothness → u_smoothness`, `_EmissionColor → u_emission`.

Defaults applied at draw: albedo white `[1,1,1,1]`, metallic 0, smoothness 0.5, emission 0, a 1×1 white texture on unit 0, real textures from unit 1 with `*_Active` / `u_use_*` flags.

Transparency rule: a surface is treated as transparent when its alpha drops below `0.999` or when the bound albedo texture contains an alpha channel (detected via image inspection).

In the editor, materials are authored through the Inspector, the material wizard, and the live material preview. Runtime property edits apply immediately without reimport.
