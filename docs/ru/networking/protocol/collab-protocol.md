# Протокол коллаборации

Совместное редактирование в редакторе: хост и гости делят курсоры, камеры, выделения, гизмо, правки сцены, скрипты и ассеты. Отдельный тракт от геймплейного неткода — строковые id пиров (`peer_<12hex>`), комнаты, парольная авторизация. Реализация — `CollaborationManager` (`core/network/collab/manager.py` + `presence.py` / `poll.py` / `watcher.py` / `scene_sync.py` / `script_sync.py` / `asset_sync.py` / `upnp.py`).

## Сервер, клиент, авторизация

`CollabServer(host="0.0.0.0", port=9876, password="", room="", max_clients=32, heartbeat_timeout=20.0)` (`serverp/base.py:47-62`, порт по умолчанию `DEFAULT_DIRECT_PORT = 9876`). Первый фрейм обязан быть `JOIN`; при переполнении отвечает `NET_KICK {"reason": "server_full"}`; затем `_auth_ok` сверяет комнату (обязана совпасть, если задана на сервере) и пароль — `token = sha256(room + "\x00" + password)` либо пароль открытым текстом — при несовпадении отвечает `AUTH_FAIL {"reason": "auth"}`; клиенты новее `PROTOCOL_VERSION + 2` получают `AUTH_FAIL {"reason": "version"}` (`serverp/base.py:174-189`, `serverp/connection.py:36-96`). Каждый пир получает цвет по кругу; входы рассылаются как `PEER_JOINED`, новичок получает `JOINED {your_id, your_color, peers, scene, version, room}`; выходы — как `LEAVE`. Защита: лимит 400 сообщ/с с пира, чистильщик протухших каждые 5 с против `heartbeat_timeout`, `PING`→`PONG`, везде `TCP_NODELAY`.

`CollabClient.connect(host, port, name, password="", room="", timeout=8.0)` шлёт `_make_join` = `{name, version, room, token}` с `TCP_NODELAY` + `SO_KEEPALIVE`; `wait_connected(timeout)` различает id и ошибку авторизации (`client.py:77-104,182-189`). `set_password` / `set_room` / `set_max_clients` переконфигурируют сервер наживую; `update_scene_data(scene)` кладёт снимок, выдаваемый новичкам (`serverp/base.py:80-87,233-234`).

## Присутствие, сцены, скрипты, ассеты

`PresenceMixin` (`collab/presence.py`): `send_cursor(x, y, hit?)`, `send_camera(pos, fwd, up)`, `send_gizmo_state(mode, hover_axis, dragging)`; негабаритные пакеты автоматически режутся на `CHUNK`-фреймы (`_send_link` / `_send_chunked`). Чужое состояние оседает в полях `RemotePeer`: `cursor_screen` / `cursor_hit`, `camera_pos`/`_fwd`/`_up`, `selected_entity_ids`, `gizmo_mode`/`_hover_axis`/`_dragging`, `current_tab` (`collab/models.py:9-34`). Каденции по умолчанию в `CollabSettings`: курсор 1/30 с, камера 1/15 с, трансформ 1/20 с, гизмо 1/10 с, пинг 3 с, опрос 8 мс, синк сцены 2 с (`collab/models.py:35-52`).

Правки сцены (`scene_sync.py` + `_route_message` сервера): `send_entity_create/delete/transform/selection/component_update/component_sync/component_add/component_remove/play_mode` — каждая рассылается под парным именем `*_UPDATE`/`*_CREATED`/… с меткой `id` отправителя, плюс трекинг вкладок `SCENE_OPEN` / `SCENE_TAB_SWITCH` / `SCENE_TAB_CLOSE`, `request_scene_snapshot()` → сервер отвечает `SCENE_SNAPSHOT` только запросившему, а `_send_full_scene_sync(target)` реплеит сущности + трансформы новому пиру. Совместные скрипты (`script_sync.py`): `send_script_open/change/cursor(pos, sel_anchor, sel_end)/ops` с колбэками `set_on_remote_script_*`. Ассеты (`asset_sync.py` + `watcher.py`): `request_asset_list` / `send_asset_list(target)` (пути + sha256-чексуммы) / `send_asset_file` / `send_asset_request`; `_AssetWatcher` опрашивает mtime (интервал ≥ 0.5 с) и дёргает колбэки изменения/удаления.

Опрос (`poll.py`): daemon-циклы `_PollThread(callback, interval_ms, stop_event)` крутят `_poll_messages`, `_send_ping` и `_sweep_peers` менеджера.

## Пример сессии

```python
mgr.start_server(host="0.0.0.0", port=9876, password="s3cret", room="LEVEL1")
invite = mgr.create_invite(public=True)          # "ZARIN1:..." (при смапленном UPnP — внешний IP)
mgr.join_invite(invite, name="Guest", password="s3cret")  # сторона гостя: direct или relay по режиму
mgr.send_cursor(x, y, hit_pos)                   # присутствие, ~30 Гц
mgr.send_script_change("scripts/player.py", text)
mgr.request_scene_snapshot()                     # подтянуть сцену целиком
mgr.stop()                                       # чистит пиров, вотчеры, UPnP-маппинги
```

См. также: Релей и UPnP (комнаты/инвайты/NAT), Типы сообщений (формат фреймов), RemoteCollaborator (аватары в сцене).

Проверено по: `core/network/serverp/base.py:47-87,89-189,204-234`; `core/network/serverp/connection.py:18-129,152-263`; `core/network/client.py:77-104,182-200`; `core/network/collab/manager.py:88-157,194-320`; `core/network/collab/presence.py:50-125,140-267`; `core/network/collab/scene_sync.py:18-100`; `core/network/collab/script_sync.py:9-59`; `core/network/collab/asset_sync.py:49-80`; `core/network/collab/watcher.py:10-73`; `core/network/collab/poll.py:8-20`; `core/network/collab/models.py:9-52`.
