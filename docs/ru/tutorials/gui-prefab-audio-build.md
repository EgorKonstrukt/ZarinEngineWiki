# GUI-меню, префабы, аудио, сборка

Четыре независимых мини-урока на одной сцене. Каждый самодостаточен.

## А. GUI-меню (Button + Label)

GUI-виджеты — ECS-компоненты, рисуемые оверлеем `GuiCanvas`; события виджетов расходятся как Python-сигналы, виджеты работают и в редакторе, и в рантайме.

1. Создаём сущность `Menu`. `Add Component` → `ButtonComponent`: в инспекторе `widget_text` (по умолчанию `"Button"`); ставим `"Start"`.
2. Создаём сущность `Score`. `Add Component` → `LabelComponent`: `widget_text` (по умолчанию `"Label"`) плюс `H Align` (`left`/`center`/`right`); ставим текст `"Score: 0"`.
3. Вешаем скрипт на `Score` (`Project → Create → Python Script` → `score.py`, `Add Component → Scripts`):

```python
class Score:
    points: int = 0

    def on_awake(self):
        self.label = self._entity.get_component_by_name("LabelComponent")

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.SPACE):
            self.points = self.points + 1
            if self.label:
                self.label.widget_text = f"Score: {self.points}"
```

4. Жмём `Play`, бьём по `Space`: метка обновляется в оверлее. Паттерн общий — скриптом рулится любое поле компонента, доступное через `get_component_by_name`.

## Б. Префабы (переиспользование `.zpep`)

1. Выбираем готовое поддерево сущностей (например, `Ball` с `Rigidbody` + `SphereCollider` + скриптом). Сохраняем как `.zpep` (Zarin Prefab) — файл хранит поддерево со стабильными GUID.
2. Инстанцируем `.zpep` в любую сцену: движок перемаппивает GUID под каждый инстанс, внутренние ссылки поддерева остаются целы.
3. Правки конкретного инстанса (значения инспектора после инстанцирования) сериализуются как оверрайды рядом с GUID префаба — сам файл префаба не трогается.
4. Сущности на префабах несут в JSON сцены `prefab_guid` / `prefab_source_id`; удаление этих ключей превращает инстанс в обычную локальную копию.

## В. Аудио (AudioSource + слушатель)

1. Создаём сущность `Music`. `Add Component` → `AudioSource`. Настоящие поля инспектора: `Clip` (`clip_path`, фильтр `*.wav *.mp3 *.ogg *.flac`), `Volume` 0–1, `Pitch` −3–3, `Loop`, `Play On Awake`, `Spatial Blend` 0–1, `Zone Shape` (sphere/box), `Min/Max Distance`, внутренний/внешний размеры бокса, кривая `Volume Rolloff`, `Offset`, времена fade in/out.
2. Указываем `Clip` на аудиофайл проекта, включаем `Loop` и `Play On Awake` — либо триггерим из скрипта:

```python
class Dj:
    def on_awake(self):
        self.src = self._entity.get_component_by_name("AudioSource")

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.M):
            if self.src:
                if self.src.is_playing:
                    self.src.stop()
                else:
                    self.src.play()
```

3. Позиционному звуку нужны уши: добавьте компонент `AudioListener` на сущность камеры. Рантайм-API: `play()` / `stop()` / `is_playing`; `stop()` при заданном `Fade Out Time` гасит плавно. Edge case: `AudioSource.on_enable` сам стартует воспроизведение, когда задан `clip_path`, — даже без `Play On Awake`, — так что повторное включение сущности перезапускает клип.
4. Жмём `Play`: с `Play On Awake` клип стартует сразу; `M` переключает. Затухание в 3D идёт по `Min/Max Distance` и кривой rolloff.

## Г. Запуск и поставка

- Запуск сцены без редактора: `python player.py SampleScene.zpes` (подходит любой путь `.zpes`; константа сцены по умолчанию — `"SampleScene.zpes"`).
- Standalone-билд: `python build_nuitka.py` (на базе Nuitka; настройки сборки — в `BuildSettings.json`). Плеер пишет диагностику в `player_diag.txt`, краш-репорты — в `crash_logs/`.
- Файлы сцен переносимы: `script_path` и ссылки на материалы/текстуры хранятся относительно корня проекта.
