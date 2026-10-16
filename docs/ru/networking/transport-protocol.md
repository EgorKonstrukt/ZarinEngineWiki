# Транспорт и протокол

Проводной слой под геймплейным неткодом и коллаборацией: TCP-сокеты, 4-байтовый big-endian префикс длины + msgpack-фреймы, типизированные сообщения, неблокирующий забор каждый кадр.

## Геймплейный транспорт (`core/network/transp/`, синглтон через `get_transport()`)

| API | Сигнатура / смысл |
|---|---|
| `host` | `host(host="0.0.0.0", port=7777, max_players=16) -> bool` — старт `GameServer` в потоке, сброс роли на server, `local_id = 0` |
| `connect` | `connect(host="127.0.0.1", port=7777, name="Player") -> bool` — старт `GameClient`, отправка `JOIN {"name"}`, ожидание до 5 с, `False` при `NET_KICK` |
| `disconnect` | `disconnect()` — стоп клиента/сервера, очистка очереди, возврат роли в `"none"` |
| `send` | `send(msg_type, data)` — клиент → серверу; в роли сервера работает как broadcast |
| `send_to` | `send_to(peer_id, msg_type, data)` — сервер → одному пиру; в роли клиента сводится к `send` серверу |
| `broadcast` | `broadcast(msg_type, data)` — алиас `send` (сервер рассылает всем пирам) |
| `poll` | `poll() -> list[(msg_type, data)]` — забрать входящие без блокировки; дёргать каждый кадр |
| `peek` / `get_peers` | `peek()` — чтение без очистки; `get_peers() -> [{id, name}]` на сервере |
| Состояние | `is_connected`, `is_server`, `is_client`, `local_id` (сервер `0`, клиент = выданный peer id), `peer_count`, `bytes_sent`, `bytes_received` |
| Капы / колбэки | `set_max_players(n)` живёт на `GameServer`, а не на `Transport`; `set_on_connected` / `set_on_disconnected` живут на `GameClient`, а не на `Transport` |

## Протокол (`core/network/protocol.py`, `core/config/constants.py`)

- Фрейминг: `make_msg(msg_type, data)` пакует msgpack `{"t": int, "d": dict}` с префиксом длины `">I"`; `FRAME_HEADER_SIZE = 4`; payload сверх `MAX_MESSAGE_SIZE` (4 МиБ) не отправляется, а бросает исключение; `parse_msg` — обратное преобразование.
- `MessageType` IntEnum: геймплейный диапазон `NET_SPAWN = 100`, `NET_DESPAWN = 101`, `NET_SPAWN_REQUEST = 102`, `NET_RPC = 103`, `NET_TRANSFORM = 104`, `NET_RIGIDBODY = 105`, `NET_ANIMATOR = 106`, `NET_OWNER_CHANGE = 107`, `NET_VARIABLES = 108`, `NET_READY = 109`, `NET_KICK = 110`, `NET_SNAPSHOT = 111` (типы коллаборации — 1–55, 210–212).
- Формы RPC: события плагина едут как `NET_RPC {"rpc": имя, "payload": {...}}`, сервер штампует `_sender` до диспатча; вызовы компонентов едут как `NET_RPC {"net_id", "method", "args", "rpc_type", "target?"}` через `rpc.send_server_rpc` / `send_client_rpc` / `send_target_rpc`.

## Транспорт коллаборации (отдельный тракт, не путать)

`CollabServer` (`core/network/serverp/base.py`, `connection.py`): `set_password` / `set_room` / `set_max_clients`, `_auth_ok` (проверка комнаты + токена), `update_scene_data`. `CollabClient` (`core/network/client.py`): `connect(host, port, name, password, room)`, `set_on_auth_failed`. Relay-хелперы (`core/network/relayp/`) и UPnP (`core/network/upnpp/`) обслуживают этот тракт. У геймплейных `GameServer`/`GameClient` ничего этого нет: первый фрейм обязан быть `JOIN`, переполнение отдаёт `NET_KICK {"reason": "server_full"}` и закрывает сокет.

## Тонкие места

Только TCP — `asyncio.start_server` / `asyncio.open_connection` с `TCP_NODELAY`, так что частый стейт платит head-of-line blocking; UDP-канала нет, шифрования нет, геймплейной аутентификации нет. Сервер ретранслирует *другим* пирам только transform/rigidbody/animator/variables/RPC/snapshot; spawn-запросы, смены владельца и despawn потребляет `NetworkManager`. `Transport.send` на сервере — это broadcast, точечная доставка только через `send_to`; клиент юникаст не умеет, он всегда шлёт серверу. `NetworkPlugin.poll()` и `NetworkManager.on_update()` осушают одну очередь синглтона, поэтому насос один на кадр (менеджер форвардит `NET_RPC {"rpc": ...}` в плагин).

Использование: `host()` один раз на хосте, `connect()` на входящих, далее `poll()` каждый кадр и диспатч по `MessageType`. Держите payload плоскими и крошечными (id, позиции, enum); крупное — через `split_chunks` (`CHUNK`) или снапшоты, никогда не возите целые сцены покадрово.

Диагностика: connect refused — сервер не стартовал или порт закрыт/файрвол; кик на входе — `server_full`; молчаливые пиры — `poll()` не дёргается каждый кадр или два насоса дерутся за одну очередь; oversize-отправка — `ValueError`, режьте payload.

Связанное: сетевой плагин (геймплейная обвязка), NetworkManager (компонент сессии), коллаборация (сессии редактора), RPC в `NetworkManager.send_rpc`.

Сверено с: `core/network/transp/transport.py:39-80,82-157`; `core/network/transp/server.py:59-66,108-124,140-160,223-263,269-317`; `core/network/transp/client.py:52-64,91-129,148-186`; `core/network/protocol.py:26-111,147-163`; `core/network/rpc.py:72-105`; `core/config/constants.py:20-21`; `core/network/serverp/base.py:80-87,174-189,233-234`; `core/components/network/netmgr/manager.py:124-172`.
