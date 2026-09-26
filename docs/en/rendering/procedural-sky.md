# Procedural Sky

Dynamic day-cycle sky computed per frame: sun disc, night stars, twilight gradients — no textures to author or stream.

| Inspector group | Covers |
|---|---|
| Procedural Night Sky | Night Sky toggle, Night Exposure |
| Stars | Density, Intensity, Scale, Twinkle, Twinkle Speed, Seed, Tint |
| Milky Way | Band density and tint controls |

A directional light with `procedural_sky_lighting` drives the sun: rotating the light moves the disc and rebalances ambient automatically, so a full day cycle is one rotation animation plus exposure keyframes.

Workflows: open-world day cycle — sun rotation 360° over the day length, night exposure low, stars fade in by sun elevation (drive density from a script); stylized dusk lock — freeze the sun low, push tint orange, keep stars faintly on; space scenes — night sky on, sun dim, strong Milky Way.

Seed variation gives different constellations per level; twinkle speed scales with exposure for stable night renders.

Troubleshooting: sun and disc disagree → coupling toggle off on the directional; stars at noon → night exposure too high; flat twilight → tint untouched, push orange/blue split.

Related: Skybox, Atmosphere, Clouds, Directional Light.
