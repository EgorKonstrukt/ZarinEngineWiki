# Обзор сетевой части

Две независимые подсистемы делят `core/network` (TCP + msgpack-фрейминг):

- Геймплейный неткод (компоненты + NetworkManager + NetworkPlugin) — реализован, реальные вызовы транспорта. `host()` / `connect()` / `send()` / `broadcast()` / `poll()` / `on_message()` в `plugins/network_plugin.py` идут в синглтон `get_transport()` из `core.network.transp` (`GameServer` / `GameClient` поверх TCP, фрейминг `MessageType`). Синхронизирует сущности сессии: трансформы, rigidbody, состояние аниматора, переменные, спавны, RPC; авторитетность на компонент (server / owner).
- Коллаборация редакторов (`CollabServer` / `CollabClient` в `core/network/serverp/*` и `core/network/client.py`, `CollaborationManager` в `core/network/collab/*`): живые курсоры, камеры, выделения, гизмо-операции, снапшоты сцен, вотчер ассетов, совместное редактирование скриптов. Парольные комнаты, relay- и UPnP-хелперы относятся к этому тракту, а не к геймплею.

Тонкие места (геймплейный тракт): только TCP, без UDP/WebRTC/ENet (`asyncio.start_server` / `asyncio.open_connection` с `TCP_NODELAY`); без пароля/комнаты/аутентификации на `GameServer` (только JOIN-хендшейк + проверка `max_players`); `MAX_MESSAGE_SIZE` 4 МиБ на фрейм; сервер ретранслирует другим пирам только `NET_TRANSFORM` / `NET_RIGIDBODY` / `NET_ANIMATOR` / `NET_VARIABLES` / `NET_RPC` / `NET_SNAPSHOT`; `poll()` — каждый кадр, один насос (плагин или менеджер), у них общая очередь синглтона `Transport`.

Страницы: транспорт и протокол, сетевой плагин, NetworkManager, NetworkIdentity, NetworkTransform, NetworkRigidbody, NetworkAnimator, NetworkVariables, NetworkSpawn, NetworkPlayer, коллаборация, RemoteCollaborator.

Рецепт первой сессии: пустая сцена → сущность NetworkManager (порт 7777, назначен player prefab, автоспавн вкл) → префаб игрока с NetworkIdentity + NetworkTransform + NetworkPlayer → host на одной машине, connect со второй → оба спавна появляются и двигаются.

Связанное: транспорт и протокол (детали провода), сетевой плагин (RPC-события), NetworkManager (диспатч сессии).

Сверено с: `plugins/network_plugin.py:64-180` (`host`/`connect`/`send`/`broadcast`/`on_message`/`poll`); `core/network/transp/transport.py:82-144`; `core/network/transp/server.py:62-66,108-116,140-160,248`; `core/network/transp/client.py:58-64,91-129`; `core/network/protocol.py:80-110`; `core/config/constants.py:20-21`; `core/network/serverp/base.py:80-87,174-189` (пароль/комната/аутентификация — только коллаборация). Без открытых маркеров в `core/network/transp/*`, `plugins/network_plugin.py`, `core/components/network/*` (grep).
