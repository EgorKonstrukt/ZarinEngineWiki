# Обзор аудио

3D positional звук поверх OpenAL: источники живут на сущностях, один слушатель — уши сцены, зоны реверберации красят пространство, а сервисы движка ведут декодирование, кэширование и покадровые обновления.

Сигнальный путь кадра:

1. `AudioListener.on_update` пушит позицию, ориентацию и скорость (конечная разность трансформа) плюс доплер-настройки в `AudioSystem`.
2. Каждый играющий `AudioSource` в `on_update` крутит фейды, опрашивает состояние OpenAL-источника, перезапускает зацикленные клипы или стреляет `on_finished` / `on_stopped`.
3. `AudioSourceManager` владеет нативными id источников: play, stop, pause, loop, spatial blend, дистанции и кривые спада.
4. `AudioSystem` владеет ресурсами: загрузка и кэширование клипов, асинхронные загрузки, состояние слушателя, доплер и скорость звука.
5. Компоненты `ReverbZone` кормят цепочку OpenAL EFX (`audio_efx`, под guard `EFXError` без расширения).

Форматы клипов (декодер): `.wav`, `.wave`, `.mp3`, `.ogg`, `.vorbis`, `.flac` (`AUDIO_EXTENSIONS` в `core/audio/miniaudio_decoder.py`). Фильтр файлов в инспекторе AudioSource уже — `Audio (*.wav *.mp3 *.ogg *.flac)` (`core/components/audio/source/component.py`). Декодирование — бэкендом miniaudio в буферы `DecodedAudio` (`decode_audio` / `decode_audio_stereo` / `decode_audio_channels`); `.wav`/`.wave` также разбираются через stdlib `wave`, если miniaudio нет (`core/audio/sys/clip.py`).

Страницы: AudioSource, AudioListener, зона реверберации, аудиосистема (менеджер, декодеры, анализатор, EFX).

Минимум для слышимого звука: одна сущность с AudioListener (обычно камера), одна с AudioSource на клип, нажать Play. Если `play_on_awake` выкл — запускайте из скрипта или включайте компонент в рантайме: включение перезапускает клип с начала.
