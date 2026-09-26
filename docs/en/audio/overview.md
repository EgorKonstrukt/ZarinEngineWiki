# Audio Overview

3D positional audio over OpenAL: sources live on entities, one listener acts as the ears, reverb zones color the space, and the engine services handle decoding, caching and per-frame updates.

Signal path per frame:

1. `AudioListener.on_update` pushes position, orientation and velocity (finite difference of the Transform) plus doppler settings into `AudioSystem`.
2. Each playing `AudioSource` advances fades in `on_update`, polls the OpenAL source state, and restarts looped clips or fires `on_finished` / `on_stopped`.
3. `AudioSourceManager` owns the native source ids: play, stop, pause, loop, spatial blend, distances and rolloff curves.
4. `AudioSystem` owns resources: clip loading and caching, async loads, listener state, doppler and speed of sound.
5. `ReverbZone` components feed the OpenAL EFX chain (`audio_efx`, guarded by `EFXError` when the extension is missing).

Supported clip formats: `.wav`, `.mp3`, `.ogg`, `.flac` (also `.aiff` / `.m4a` through the script resource filter). Decoding is handled by the miniaudio backend into `DecodedAudio` buffers.

Pages: AudioSource, AudioListener, Reverb Zone, Audio System (manager, decoders, analyzer, EFX).

Setup minimum for audible sound: one entity with AudioListener (usually the camera), one entity with AudioSource pointing at a clip, press Play. If `play_on_awake` is off, start playback from a script or enable the component at runtime — enabling replays the clip from the start.
