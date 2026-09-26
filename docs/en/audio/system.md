# Audio System

Engine services behind the components: resources, decoding, per-source state and the EFX chain.

Modules (`core/audio`):

- `audio_system.AudioSystem` — singleton: clip loading and caching, async loads, listener position/orientation/velocity setters, doppler factor and speed of sound. Components never touch OpenAL directly; they call here.
- `audio_system.AudioSourceManager` — singleton: owns native source ids and per-source state — play, stop, pause, loop, volume, pitch, spatial blend, distances, rolloff curves, zone shapes and box sizes.
- `miniaudio_decoder.DecodedAudio` — decode backend turning `.wav` / `.mp3` / `.ogg` / `.flac` files into PCM buffers ready for OpenAL upload.
- `audio_analyzer` — analysis helpers (spectrum and level followers) for visualizers and beat-reactive effects.
- `audio_efx` — EFX extension wrapper for reverb zones; `EFXError` signals a missing extension so the engine can fall back to dry output.

Data flow for a new clip: `AudioSource.play` → manager `play(...)` with all spatial parameters → system resolves the clip from cache or decodes async → native source configured (gain, pitch, looping, position, rolloff) → component polls state each frame.

Resource discipline: clips are cached by path, so ten sources sharing one file decode once; long music tracks stream rather than fully preload; releasing the last source frees the native id while the decoded buffer stays cached for instant replay.

Gizmo support: audio components declare the `audio` gizmo pass with zone shapes and distance rings, so levels can be mixed visually in the viewport.

Related: AudioSource, AudioListener, Reverb Zone, Curve, Profiler (audio_ms readout).
