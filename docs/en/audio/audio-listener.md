# AudioListener

The ears of the scene. Attach to the camera entity; the listener Transform defines what the player hears and from where.

| Field | Default | Inspector range | Meaning |
|---|---|---|---|
| doppler_factor | 1.0 | 0–10 | Pitch shift from relative motion |
| speed_of_sound | 343.3 | 0.1–10000 | m/s reference for doppler math |

Per-frame behavior, verified from code:

- `on_start` pushes both values into `AudioSystem` once.
- `on_update` re-pushes both values, then sets listener position from the Transform, orientation from Transform forward/up, and velocity as the finite position difference over `dt` (zero on the first frame).
- Both fields serialize into the scene (`doppler_factor`, `speed_of_sound`) with the same defaults on load.

Guidance:

- Exactly one active listener per scene. Two listeners fight over the singleton state every frame.
- Parent the listener to the player camera so rotation affects panning and doppler follows movement.
- Exaggerate `doppler_factor` (2–4) for arcade fly-bys; keep 1.0 for realism. Underwater scenes lower `speed_of_sound` toward ~1480 for water.
- Sources with `spatial_blend` 0 ignore the listener position; blend 1 sources pan and attenuate fully.

Related: AudioSource, Audio System, Camera.
