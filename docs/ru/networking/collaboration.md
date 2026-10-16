# Коллаборация

Real-time сессии нескольких редакторов поверх TCP с msgpack-фреймингом: несколько людей, один проект, живые курсоры, камеры, выделения и гизмо-операции.

Что синкается вживую: курсоры и камеры вьюпортов, выделения сущностей, гизмо-драги, снапшоты сцен по требованию, изменения ассетов через вотчер файлов, открытые вкладки, содержимое скриптов с покусорными правками через operational-transform.

Точки входа (`core/network/collab/*`, `core/network/client.py`, `core/network/serverp/*` — единого `collaboration.py` нет):

- `CollabServer` (`serverp/base.py` + `connection.py`): хостит комнату — `set_password` / `set_room` / `set_max_clients`, проверка `_auth_ok` (комната+токен), реестр пиров с колбэками входа/выхода (`set_peer_joined_callback`, `set_peer_left_callback` в `collab/presence.py`), раздача снапшотов (`set_scene_snapshot_callback`), broadcast удалённых событий сцен/вкладок/скриптов.
- `CollabClient` (`core/network/client.py`): входит — `connect(host, port, name, password, room)`, `set_on_auth_failed` / `set_on_connected` / `set_on_disconnected`, `send()` + `poll_messages()`; отправители открытия/изменения/курсора скриптов (`send_script_open/change/cursor` в `collab/script_sync.py`), приёмники удалённого открытия сцены, переключения/закрытия вкладок, операций скриптов.
- Фоновый `_PollThread` (`collab/poll.py`) осушает сокет без блокировки редактора; `_AssetWatcher` (`collab/watcher.py`) пушит изменения файлов.
- CollaborationManager (`collab/manager.py`, 449 строк, сторона редактора) владеет синком состояния (`start_server(host, port, password, room)`, `connect(...)`, `host_relay` / `join_relay`, `create_invite` / `create_lan_invite` / `join_invite`, `stop`), discovery пиров и таймаутами дисконнекта; панель даёт host/join, список пиров и kick.

Поток сессии: хост стартует CollabServer из панели коллаборации → пиры входят по адресу → камеры и курсоры каждого появляются гизмо RemoteCollaborator → правки broadcastятся операциями → конфликты резолвятся порядком операций, снапшоты лечат расхождения.

Этикет против боли: поддерево сцены владеет один пир за раз (broadcast выделения показывает кто); тяжёлые реимпорты ждут обратного отсчёта; конфликты скриптов предпочитают поток операций, а не copy-paste поверх.

Диагностика: пир невидим — файрвол или неверный адрес; правки потеряны — расхождение beyond op-stream, запросите свежий снапшот через `set_on_scene_sync`; высокая задержка — вотчер флудит на огромных импортах, исключите build/cache.

Связанное: RemoteCollaborator (гизмо пиров), транспорт и протокол (проводной слой), панель VCS (коммит наколлаборированного).
