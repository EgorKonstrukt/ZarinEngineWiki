# Обзор уроков

Три практических руководства. Читайте по порядку; каждое опирается на предыдущее.

Общие требования:

1. Установите зависимости (`pip install -r requirements.txt`) и откройте редактор (`python main.py`). Настройки проекта не требуется.
2. Выучите четыре точки UI, используемые везде: панель `Hierarchy` (дерево сцены) → `Inspector → Add Component` (диалог `Add Component`) → правка полей в `Inspector` → `Play` для запуска.
3. Скрипты создаются через `Project → Create → Python Script` (`.py`) и вешаются через `Inspector → Add Component → Scripts` с выбором файла в поле `Script`. Значения из инспектора хранятся в сцене (`.zpes`), а не в `.py`-файле.

Руководства:

- Скрипты Rotator и Mover — вращение и WASD-движение через `Transform.rotate` / `translate`, `Input`/`KeyCode`, настраиваемый `speed`. Начните отсюда.
- Физическая сцена — земля и мяч на `Rigidbody` + `BoxCollider`, силы в `on_fixed_update`, подсчёт ударов в `on_collision_enter`.
- GUI-меню, префабы, аудио, сборка — `ButtonComponent`/`LabelComponent`, переиспользование `.zpep`, `AudioSource.play()`, запуск `python player.py Scene.zpes`, standalone-сборка `python build_nuitka.py`.

Соглашения: `self._entity` — владеющая сущность (движок выставляет перед каждым вызовом); `get_component_by_name("...")` забирает соседний компонент по зарегистрированному имени (`"Transform"`, `"Rigidbody"`, `"AudioSource"`, `"LabelComponent"`); вход вращения — `Vec3` градусов, применяемый методом `Transform.rotate`, и это единственный `rotate` в скриптинге — у самого `Vec3` метода `rotate` нет, только `dot`, `cross`, `length`, `normalized`, `lerp` и другие.
