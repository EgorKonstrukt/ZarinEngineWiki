# Water

Water surfaces from calm ponds to infinite oceans.

| Inspector group / field | Meaning |
|---|---|
| Water: Water Shader | Surface shader (`Water` / `WaterSim`) |
| Surface Type | Still, river, ocean presets path |
| Infinite Ocean / Ocean Size | Endless plane and its scale |
| Waves | Wave set controls |
| Deep / Shallow / Foam / SSS / Horizon Color | Color ramp |
| Smoothness / Distortion / Detail Normal | Surface response |

Swimming bodies use the Buoyancy component; the liquid region itself is marked by the WaterVolume environment component. Caustics and underwater rendering come from the matching internal shaders.

Related: Buoyancy, WaterVolume notes, Skybox (reflections), Fog.
