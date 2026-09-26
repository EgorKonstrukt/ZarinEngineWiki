# Clouds

Volumetric-style cloud layer with coverage, density, wind drift and sun response — skies with weather, not wallpapers.

| Inspector group / field | Meaning |
|---|---|
| Cloud: Cloud Material | Shading material built on the `Clouds` / `CloudLayer` shaders |
| Coverage / Density | Sky fill fraction and layer thickness |
| Wind Speed / Wind Direction | Drift vector |
| Height / Thickness / Noise Scale | Layer placement and puff granularity |
| Opacity / Shadow Strength | Look weight and how much sun they block |
| Fog Density / Height Falloff / Scattering | Blend into the atmospheric haze |

Shadowed clouds darken the scene consistently with the sun position — overcast lighting comes free once strength is tuned. Animate wind slowly (0.1–0.5 units) for living skies; fast values read as time-lapse.

Pairs with Procedural Sky or Skybox backgrounds. Coverage near 1 with high density gives storm ceilings; low coverage with high scattering gives fair-weather dots.

Troubleshooting: invisible → coverage zero or opacity zero; flat white → density maxed with no noise scale variation; no scene darkening → shadow strength zero.

Related: Procedural Sky, Skybox, Atmosphere, Directional Light.
