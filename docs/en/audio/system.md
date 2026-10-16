# Audio System

Engine services behind the components: resources, decoding, per-source state and the EFX chain.

Modules (`core/audio`):

- `sys/system.py: AudioSystem` — singleton: clip loading and caching (`load_clip` / `load_clip_async` / `get_clip`, cache keyed by absolute path + `.import` mtime), listener position/orientation/velocity setters, doppler factor and speed of sound, device enumeration. Components never touch OpenAL directly; they call here.
- `sys/sources.py: AudioSourceManager` — singleton: owns native source ids and per-source state — `play`, `stop`, `pause`, `stop_all`, volume, pitch, spatial blend, distances, rolloff curves, zone shapes and box sizes; enforces `audio.max_sources` (default 32).
- `sys/clip.py: AudioClip` — `load_from_file` (honors sidecar `<clip>.import` `quality` 0–100 via `_resample`, floor 8000 Hz), `create_buffer`, `ensure_pcm` / `ensure_stereo_pcm`.
- `miniaudio_decoder.DecodedAudio` — decode backend turning `.wav` / `.wave` / `.mp3` / `.ogg` / `.vorbis` / `.flac` files into PCM buffers ready for OpenAL upload.
- `audio_analyzer` — analysis helpers (spectrum and level followers) for visualizers and beat-reactive effects.
- `efx/` (`efx.py`, `filters.py`, `slots.py`) — EFX extension wrapper for reverb zones; `EFXError` signals a missing extension so the engine can fall back to dry output.

Data flow for a new clip: `AudioSource.play` → manager `play(...)` with all spatial parameters → system resolves the clip from cache (`load_clip`) or decodes on the audio thread pool (`load_clip_async` callback) → native source configured (gain, pitch, looping, position, rolloff) → component polls state each frame.

Resource discipline: clips are fully decoded into PCM buffers and cached by absolute path (invalidated on `.import` mtime change), so ten sources sharing one file decode once; releasing the last source frees the native id (`stop` / `stop_all`) while the decoded buffer stays cached for instant replay.

Gizmo support: audio components declare the `audio` gizmo pass with zone shapes and distance rings, so levels can be mixed visually in the viewport.

Related: AudioSource, AudioListener, Reverb Zone, Curve, Profiler (audio_ms readout).
