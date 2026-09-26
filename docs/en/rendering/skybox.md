# Skybox

Static HDRI environment background with image-based lighting contribution for PBR materials.

| Inspector group / field | Meaning |
|---|---|
| HDRI Skybox: HDRI / EXR | Environment image asset |
| HDRI Skybox: Show Background | Draw the image as the visible sky |
| HDRI Skybox: Affect Environment (IBL) | Light PBR materials from the image |
| Image / Exposure (EV) / Intensity / Tint / Saturation | Look controls |
| Background Blur | Soften the visible sky (cheap depth-of-field feel) |
| Flip Y, Orientation, Rotation Yaw/Pitch/Roll (deg) | Mapping controls |

Use Skybox for baked, art-directed environments (interiors, stylized scenes, photo plates) and Procedural Sky for dynamic day cycles. Match ambient values to the chosen sky or materials drift.

Rotation yaw animates dusk-to-dawn illusions on static HDRIs; exposure rides the day curve alongside the directional intensity.

Troubleshooting: black sky → image path broken or Show Background off; materials too dark under HDRI → Affect Environment off or exposure low; seam line → orientation/flip mismatch with the capture, toggle Flip Y.

Related: Procedural Sky, Clouds, Atmosphere, Directional Light, Materials (IBL response).
