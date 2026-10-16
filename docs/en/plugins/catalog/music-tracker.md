# Tracker Music (TrackerMusicPlugin)

Tracker-module music: MOD/XM/S3M/IT playback rendered to WAV by the plugin's own software synthesis engine in `rend/`, plus a pattern editor dock.

| NAME | VERSION | SYSTEM |
|---|---|---|
| `TrackerMusicPlugin` | `1.0.0` | `False` |

Declared in `plugins/tracker_music_plugin/__init__.py` (packaging manifest `plugins/tracker_music_plugin/manifest.json`, `entry: tracker_music_plugin/__init__.py`). Registers the `TrackerAudioSource` component, dock `"Tracker Editor"` (area `bottom`, tab `Audio`), menu `Edit → Open Tracker Editor`, toolbar button `Tracker`, and file openers for `.mod .xm .s3m .it`.

## Files (the `rend/` engine — not nodmod)

- `plugins/tracker_music_plugin/rend/engine.py` — `TrackerSoftwareRenderer(_SeqMixin, _FxMixin, _MixMixin)`, `METER_BLOCK_SEC = 0.1`.
- `plugins/tracker_music_plugin/rend/engine_seq.py` — sequencer mixin: `sample_rate = 44100`, `voices`, `n_channels`, `channel_gains`, `master_gain`, `set_channel_db(ch, db)`, `get_meter_data()`.
- `plugins/tracker_music_plugin/rend/engine_mix.py` — numpy voice mixer (`_mix_one`, `_render_voice_numpy`).
- `plugins/tracker_music_plugin/rend/engine_fx.py` — instrument/sample helpers (`_sample_for_instrument`, `_sample_volume`, `_sample_wave`, `_sample_loop`).
- `plugins/tracker_music_plugin/rend/defs.py` — constants `_NTSC_SR = 8287.0`, `_AMIGA_CLOCK = 3546894.6`, `_RENDER_VERSION = 3`, `_MOD_PERIODS`, `_NOTE_NAMES`, plus `_note_to_midi()` and `_parse_effect()`.
- `plugins/tracker_music_plugin/rend/service.py` — `TrackerRenderService(cache_dir, sample_rate=44100)`, `load_module(path)`, `render_to_wav_file(pcm, path, sample_rate)`, `find_external_renderer()`.
- `plugins/tracker_music_plugin/rend/voice.py` — `_Voice`.
- `plugins/tracker_music_plugin/tracker_audio_source.py` — `TrackerAudioSource(AudioSource)` component + `patch_audio_source_play()`.
- Optional Cython mixer: `fastmix.pyx` / `fastmix.c`, loaded via `import_extension` (`core/foundation/plugin_cython.py`), built by `ensure_extensions`.

Note: module *parsing* is delegated to the vendored `plugins/tracker_music_plugin/libs/nodmod` (`load_song`); all *synthesis* is the `rend/` engine above.

## API

`TrackerRenderService.render_wav(module_path, quality="software")` → cached `.wav` path (key includes file hash, sample rate, `_RENDER_VERSION`) or `None`. `quality="external"` tries `openmpt123`/`ffmpeg` first, then falls back to the software renderer (`TrackerSoftwareRenderer(sample_rate).render(song)` → stereo 16-bit WAV).

`TrackerAudioSource` fields: `clip_path` (module file), `render_quality` (`"software"` / `"external"`), readonly `song_length_sec`, `channels_count` (filled from `song.get_song_info()`). Supported extensions (`TRACKER_EXTS`): `.mod .xm .s3m .it .669 .mtm .stm .ult`. `play()` renders the module to WAV and plays it through `AudioSource`, restoring the original `clip_path` afterwards. The plugin also patches stock `AudioSource.play` (`ComponentPatcher.wrap_method`, before/after) so a plain `AudioSource` with a tracker `clip_path` renders automatically too.

## Playing a track — example

```python
class Jukebox:
    module: str = "assets/music/level1.xm"

    def on_start(self):
        src = self._entity.get_component_by_name("TrackerAudioSource")
        if src is None:
            return
        src.clip_path = self.module
        src.render_quality = "software"  # or "external" for openmpt123/ffmpeg
        src.play()
        Logger.info(f"Playing: {src.channels_count} ch, {src.song_length_sec:.1f} s")
```

Steps: add `TrackerAudioSource` to an entity → set `Module` to the `.xm`/`.mod` file → press Play (or call `play()`). Open `Edit → Open Tracker Editor` (or double-click a module file) to inspect patterns via `song.get_song_info()`.

## Verified against

`plugins/tracker_music_plugin/__init__.py`, `plugins/tracker_music_plugin/manifest.json`, `plugins/tracker_music_plugin/tracker_audio_source.py`, `plugins/tracker_music_plugin/rend/__init__.py`, `plugins/tracker_music_plugin/rend/engine.py`, `plugins/tracker_music_plugin/rend/engine_seq.py`, `plugins/tracker_music_plugin/rend/engine_mix.py`, `plugins/tracker_music_plugin/rend/engine_fx.py`, `plugins/tracker_music_plugin/rend/defs.py`, `plugins/tracker_music_plugin/rend/service.py`.
