# Reverb Zone

Spatial reverberation over OpenAL EFX: rooms, halls, caves and tunnels that color every source passing through.

| Inspector group | Covers |
|---|---|
| Zone | Min Distance (default 10.0), Max Distance (default 15.0) — full-effect and falloff radii |
| Reverb Preset | Named starting points for common spaces (default `Generic`; `Off` bypasses, `User` marks hand-tuned params) |
| Room | Room level (default −1000), Room HF (default −100), Room LF (default 0) — low/mid/high decay balance |
| Decay | Decay Time (default 1.49), Decay HFRatio (default 0.83) — length and brightness of the tail |
| Reflections | Reflections level (default −2602), Reflections Delay (default 0.007) — early echoes |
| Reverb | Reverb level (default 200), Reverb Delay (default 0.011) — late diffuse field |
| Reference | HFReference (default 5000), LFReference (default 250) — crossover frequencies |
| Rolloff | Room Rolloff Factor (default 0.0) — distance response of the effect |
| Mix | Diffusion (default 100.0), Density (default 100.0) — echo density / modal richness |

Source: `core/components/audio/reverb/component.py` (`_inspector_fields`, `__init__` defaults, `serialize`/`deserialize` with legacy linear→mB migration).

Workflow:

1. Add the component to an empty entity marking the space center.
2. Pick the closest preset, set Min Distance to cover the room, Max Distance to feather the edge.
3. Tune Decay Time first (small room ~0.3 s, hall ~1.5 s, cave ~3 s), then early reflections for slap, then late level for wash.
4. Walk the listener through with a looping source to verify the transition in and out.

The EFX path is guarded: without the extension the engine raises `EFXError` internally and continues dry — check the console if a zone sounds flat on some machines.

Related: AudioSource (spatial blend feeds the send), AudioListener (listener position drives zone routing), Audio System (EFX module), Fog (visual twin for caves).
