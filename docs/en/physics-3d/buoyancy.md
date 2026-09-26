# Buoyancy

Floating bodies for ponds, rivers and oceans: hulls displace water, waves rock them, currents push them.

| Inspector group / field | Meaning |
|---|---|
| Buoyancy: Object Density | Hull density vs water — below water density floats |
| Override Volume | Manual displaced volume, skips auto computation |
| Water Density | Liquid density (fresh vs salt vs lava-thick) |
| Hydro Drag / Hydro Angular Drag | Water damping on motion and spin |
| Flow Influence | How much currents push the hull |
| Options: Use Waves | Sample the wave surface for rocking |
| Options: Use Flow | Sample currents for drift |
| Sample Resolution | Probe density over the hull |
| Max Accel (x g) | Stability clamp on water forces |
| Debug Draw | Volume and probe visualization |

Pair with a WaterVolume environment component marking the liquid region and a Water surface for visuals — the component alone floats nothing without a defined liquid.

Tuning order: density until the waterline sits right (deck awash = too dense), hydro drag until stopping feels weighty, waves on for rocking, flow for rivers. Light fast boats want low drag and high max accel; barges want the opposite.

Sample resolution trades fidelity for cost: high for long hulls (probes along the length catch pitch), low for barrels. Debug Draw shows probes — if they miss the hull, the forces will too.

Troubleshooting: sinks → density above water or volume underestimated, check override; vibrates → max accel too high or resolution too low; ignores waves → Use Waves off or outside the WaterVolume; drifts sideways forever → flow influence with an unbalanced current field.

Related: Water, WaterVolume notes, Soft Body, Rigidbody (mass interplay).
