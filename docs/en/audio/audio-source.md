# AudioSource

A playable voice on an entity: clip, loudness, pitch, looping, 3D position, distance falloff, fades and finish callbacks.

| Inspector group / field | Default | Meaning |
|---|---|---|
| Playback: Clip | — | Audio file (wav/mp3/ogg/flac) |
| Playback: Volume | 1.0 | Loudness multiplier |
| Playback: Pitch | 1.0 | Playback rate, −3…3 |
| Playback: Loop | off | Restart at the end |
| Playback: Play On Awake | off | Auto-start in `on_start` |
| Spatial: Spatial Blend | 1.0 | 0 = flat 2D, 1 = full 3D |
| Spatial: Zone Shape | sphere | SPHERE or BOX attenuation volume |
| Spatial: Volume Rolloff | linear | Curve editor, default keys `[[0,1],[1,0]]` |
| Spatial: Min / Max Distance | 1.0 / 50.0 | Full-volume radius and silence radius |
| Spatial: Box Inner / Outer Size | 2³ / 10³ | BOX zone full-volume and falloff extents |
| Fades: Offset (sec) | 0.0 | Start position inside the clip |
| Fades: Fade In / Fade Out Time | 0.0 | Automatic ramps on play / stop |

Lifecycle, verified from code:

- `on_start` plays when `play_on_awake` is set, a clip is assigned and nothing is playing.
- `on_enable` replays the clip whenever the component is enabled with a clip assigned.
- `on_disable` / `on_destroy` stop the native source immediately.
- `on_update` advances active fades, polls `AL_SOURCE_STATE`, restarts looped clips, and fires `on_finished` (natural end) or `on_stopped` (stopped, fade-out completed). Callback exceptions are logged, never thrown.

Script API:

```python
src = self._entity.get_component_by_name("AudioSource")

def on_start(self):
    src = self._entity.get_component_by_name("AudioSource")
    if src:
        src.on_finished = self.next_track
        src.play()

def next_track(self):
    src = self._entity.get_component_by_name("AudioSource")
    if src:
        src.stop()

def on_update(self, dt):
    src = self._entity.get_component_by_name("AudioSource")
    if src and Input.GetKeyDown(KeyCode.M):
        if src.is_playing:
            src.stop()
        else:
            src.play()
```

Details:

- `play()` is a no-op without a clip or while already playing; it forwards volume, pitch, loop, spatial blend, distances, rolloff, offset and zone shape to the manager and arms `fade_in_time` through a zero-gain start.
- `stop()` with `fade_out_time` set performs a fade-out first and only then releases the source; without it the source stops immediately. `fade_in(duration)` / `fade_out(duration)` are also callable directly.
- `is_playing` reflects the component state flag.
- Rolloff presets: `linear_preset()` returns `[[0,1],[1,0]]`; `logarithmic_preset()` builds `1/(1+2t)` keys at 0.1 steps for a natural distance falloff.
- Zone evaluation is sphere-based by default (`min_distance` full volume, `max_distance` silence); BOX mode uses inner size as full volume and outer size as the falloff boundary. The gizmo draws the active shape in the viewport.

Troubleshooting: no sound → check clip path resolves, AudioSystem exists (openal installed), listener present, volume and spatial blend nonzero, listener within max distance; clicks on stop → set a small fade_out_time; loop gap → keep loop on and avoid re-calling play every frame.

Related: AudioListener, Reverb Zone, Audio System, Curve (rolloff editor).
