# Трекерная музыка (TrackerMusicPlugin)

Музыка из трекерных модулей: MOD/XM/S3M/IT проигрываются через рендер в WAV собственным программным движком синтеза в `rend/`, плюс док-редактор паттернов.

| NAME | VERSION | SYSTEM |
|---|---|---|
| `TrackerMusicPlugin` | `1.0.0` | `False` |

Объявлен в `plugins/tracker_music_plugin/__init__.py` (манифест упаковки `plugins/tracker_music_plugin/manifest.json`, `entry: tracker_music_plugin/__init__.py`). Регистрирует компонент `TrackerAudioSource`, док `"Tracker Editor"` (область `bottom`, вкладка `Audio`), меню `Edit → Open Tracker Editor`, кнопку тулбара `Tracker` и открывалки файлов `.mod .xm .s3m .it`.

## Файлы (движок `rend/` — не nodmod)

- `plugins/tracker_music_plugin/rend/engine.py` — `TrackerSoftwareRenderer(_SeqMixin, _FxMixin, _MixMixin)`, `METER_BLOCK_SEC = 0.1`.
- `plugins/tracker_music_plugin/rend/engine_seq.py` — миксин секвенсора: `sample_rate = 44100`, `voices`, `n_channels`, `channel_gains`, `master_gain`, `set_channel_db(ch, db)`, `get_meter_data()`.
- `plugins/tracker_music_plugin/rend/engine_mix.py` — numpy-микшер голосов (`_mix_one`, `_render_voice_numpy`).
- `plugins/tracker_music_plugin/rend/engine_fx.py` — хелперы инструментов/сэмплов (`_sample_for_instrument`, `_sample_volume`, `_sample_wave`, `_sample_loop`).
- `plugins/tracker_music_plugin/rend/defs.py` — константы `_NTSC_SR = 8287.0`, `_AMIGA_CLOCK = 3546894.6`, `_RENDER_VERSION = 3`, `_MOD_PERIODS`, `_NOTE_NAMES`, функции `_note_to_midi()` и `_parse_effect()`.
- `plugins/tracker_music_plugin/rend/service.py` — `TrackerRenderService(cache_dir, sample_rate=44100)`, `load_module(path)`, `render_to_wav_file(pcm, path, sample_rate)`, `find_external_renderer()`.
- `plugins/tracker_music_plugin/rend/voice.py` — `_Voice`.
- `plugins/tracker_music_plugin/tracker_audio_source.py` — компонент `TrackerAudioSource(AudioSource)` + `patch_audio_source_play()`.
- Опциональный Cython-микшер: `fastmix.pyx` / `fastmix.c`, загрузка через `import_extension` (`core/foundation/plugin_cython.py`), сборка через `ensure_extensions`.

Нюанс: *парсинг* модулей делегирован вендорному `plugins/tracker_music_plugin/libs/nodmod` (`load_song`); весь *синтез* — движок `rend/` выше.

## API

`TrackerRenderService.render_wav(module_path, quality="software")` → путь к кэшированному `.wav` (ключ включает хеш файла, частоту и `_RENDER_VERSION`) или `None`. `quality="external"` сначала пробует `openmpt123`/`ffmpeg`, затем откатывается на программный рендер (`TrackerSoftwareRenderer(sample_rate).render(song)` → стерео WAV 16 бит).

Поля `TrackerAudioSource`: `clip_path` (файл модуля), `render_quality` (`"software"` / `"external"`), readonly `song_length_sec`, `channels_count` (из `song.get_song_info()`). Поддерживаемые расширения (`TRACKER_EXTS`): `.mod .xm .s3m .it .669 .mtm .stm .ult`. `play()` рендерит модуль в WAV и играет через `AudioSource`, после возвращая исходный `clip_path`. Плагин также патчит `AudioSource.play` (`ComponentPatcher.wrap_method`, before/after), так что обычный `AudioSource` с трекерным `clip_path` тоже рендерится автоматически.

## Как проиграть трек — пример

```python
class Jukebox:
    module: str = "assets/music/level1.xm"

    def on_start(self):
        src = self._entity.get_component_by_name("TrackerAudioSource")
        if src is None:
            return
        src.clip_path = self.module
        src.render_quality = "software"  # или "external" для openmpt123/ffmpeg
        src.play()
        Logger.info(f"Playing: {src.channels_count} ch, {src.song_length_sec:.1f} s")
```

Шаги: добавьте `TrackerAudioSource` на сущность → укажите `Module` (`.xm`/`.mod`) → Play (или вызовите `play()`). `Edit → Open Tracker Editor` (или дабл-клик по модулю) открывает паттерны через `song.get_song_info()`.

## Проверено по коду

`plugins/tracker_music_plugin/__init__.py`, `plugins/tracker_music_plugin/manifest.json`, `plugins/tracker_music_plugin/tracker_audio_source.py`, `plugins/tracker_music_plugin/rend/__init__.py`, `plugins/tracker_music_plugin/rend/engine.py`, `plugins/tracker_music_plugin/rend/engine_seq.py`, `plugins/tracker_music_plugin/rend/engine_mix.py`, `plugins/tracker_music_plugin/rend/engine_fx.py`, `plugins/tracker_music_plugin/rend/defs.py`, `plugins/tracker_music_plugin/rend/service.py`.
