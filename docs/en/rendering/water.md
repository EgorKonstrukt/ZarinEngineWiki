# Water

Water surfaces from calm ponds to infinite oceans, shaded by the `Water` / `WaterSim` shaders.

| Inspector group / field | Meaning |
|---|---|
| Water: Water Shader | Surface shader selection |
| Surface Type | Still, river, ocean preset paths |
| Infinite Ocean / Ocean Size | Endless plane toggle and its scale |
| Waves | Wave set amplitude/frequency controls |
| Deep / Shallow / Foam / SSS / Horizon Color | Full color ramp from depth to horizon |
| Smoothness / Distortion / Detail Normal | Surface response and refraction wobble |

Swimming bodies use the Buoyancy component; the liquid region itself is marked by the WaterVolume environment component; caustics and underwater rendering come from the matching internal shaders.

Workflows: pond — small plane, still preset, high smoothness for mirror reflections of the skybox; river — stretched plane, flow direction waves, foam color at banks; ocean — infinite on, size matched to far plane, horizon color fused with fog.

Foam placement follows depth (shallow color band) — align the terrain shelf with the shallow range or foam floats wrongly. Distortion sells refraction; overdo it and text behind water becomes unreadable.

Troubleshooting: black water → shader or reflection source broken, check skybox/ambient; no buoyancy → WaterVolume missing, the surface alone does not float bodies; foam everywhere → shallow range too wide for the shelf.

Related: Buoyancy, Skybox (reflections), Fog, PostFX (underwater grading twin).
