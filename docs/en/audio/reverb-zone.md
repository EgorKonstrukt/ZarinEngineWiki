# Reverb Zone

Spatial reverberation over OpenAL EFX: rooms, halls, caves and tunnels that color every source passing through.

| Inspector group | Covers |
|---|---|
| Zone | Min Distance, Max Distance — full-effect and falloff radii |
| Reverb Preset | Named starting points for common spaces |
| Room | Room level, Room HF, Room LF — low/mid/high decay balance |
| Decay | Decay Time, Decay HFRatio — length and brightness of the tail |
| Reflections | Reflections level, Reflections Delay — early echoes |
| Reverb | Reverb level, Reverb Delay — late diffuse field |
| Reference | HFReference, LFReference — crossover frequencies |
| Rolloff | Room Rolloff Factor — distance response of the effect |

Workflow:

1. Add the component to an empty entity marking the space center.
2. Pick the closest preset, set Min Distance to cover the room, Max Distance to feather the edge.
3. Tune Decay Time first (small room ~0.3 s, hall ~1.5 s, cave ~3 s), then early reflections for slap, then late level for wash.
4. Walk the listener through with a looping source to verify the transition in and out.

The EFX path is guarded: without the extension the engine raises `EFXError` internally and continues dry — check the console if a zone sounds flat on some machines.

Related: AudioSource (spatial blend feeds the send), Audio System (EFX module), Fog (visual twin for caves).
