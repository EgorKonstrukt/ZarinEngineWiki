# Внутреннее устройство транспорта

Как `GameServer` / `GameClient` двигают байты: одно TCP-соединение на пира, у каждого свой цикл `asyncio` в daemon-потоке, запись с дренированием. Доступ-синглтон через `get_transport()`.

## `GameServer` (`core/network/transp/server.py`)

`start()` поднимает поток цикла и ждёт флаг готовности до 5 с; `set_max_players(n)` ставится в любой момент с клампом `>= 1`. Слушатель — `asyncio.start_server` с `TCP_NODELAY` и на слушающих сокетах, и на каждом принятом (`server.py:108-116,126-133`). `_read_frame` читает ровно `FRAME_HEADER_SIZE` байт, распаковывает `">I"`, отбрасывает `0` и `> MAX_MESSAGE_SIZE`, затем читает тело (`server.py:118-124`).

Рукопожатие — первый фрейм решает всё:

1. Первый фрейм обязан быть `JOIN`, иначе сокет молча закрывается (`server.py:140-147`).
2. При переполнении (`len(clients) >= max_players`) сервер пишет `NET_KICK {"reason": "server_full"}`, дренирует и закрывает (`server.py:148-160`).
3. Иначе выдаёт `peer_id` из `_next_id` (с 1), обрезает имя до 32 символов, кладёт `PEER_JOINED {"id", "name"}` в локальную очередь, рассылает его существующим пирам и отвечает новичку `JOINED {"your_id", "peers"}` (`server.py:161-188`).
4. При отключении кладёт `LEAVE {"id"}` локально и рассылает остальным, затем закрывает сокет (`server.py:194-221`).

`_client_loop` отвечает на `PING` пакетом `PONG {"t"}` сразу; остальные принятые типы получают метку `_sender`, складываются в общую входящую очередь и — только transform/rigidbody/animator/variables/RPC/snapshot — перепаковываются и пишутся всем *остальным* пирам (`server.py:223-267`). `broadcast` пишет одно упакованное сообщение всем клиентам; `send_to(peer_id, ...)` — одному; дренирование в обоих случаях планируется на цикле через `run_coroutine_threadsafe` (`server.py:269-317`).

## `GameClient` (`core/network/transp/client.py`)

`connect(host, port, name)` поднимает поток цикла (no-op, если уже подключён). `_connect` использует `asyncio.open_connection`, ставит `TCP_NODELAY`, помечает соединение и шлёт `JOIN {"name"}` **первым** фреймом, затем ждёт ответ (`client.py:91-104`). `JOINED` сохраняет `peer_id` и кладёт фрейм в очередь; `NET_KICK` кладётся в очередь и сессия завершается; непарсящийся ответ обрывает подключение. `_read_loop` отвечает на `PING` пакетом `PONG`, при `NET_KICK` кладёт его в очередь и выходит, остальное складывает в очередь (`client.py:148-177`). `send(msg_type, data)` упаковывает и планирует `_async_send` только при живом соединении и цикле; `bytes_sent` / `bytes_received` считают фреймированные байты. `set_on_connected` / `set_on_disconnected` — единственные колбэки, они живут на `GameClient`, а не на `Transport`.

## Фасад `Transport` (`core/network/transp/transport.py:82-144`)

| Метод | Семантика |
|---|---|
| `host(host="0.0.0.0", port=7777, max_players=16)` | Сначала disconnect, затем старт `GameServer`; при успехе роль server, `local_id = 0` |
| `connect(host="127.0.0.1", port=7777, name="Player")` | Сначала disconnect, затем старт `GameClient`; ждёт `connected` до **5 с**, при `NET_KICK` сразу `False` |
| `disconnect()` | Останавливает клиент и сервер, роль назад в `"none"`, чистит входящую очередь |
| `send(msg_type, data)` | Клиент → серверу; на сервере работает как **broadcast** |
| `send_to(peer_id, msg_type, data)` | Сервер → одному пиру; на клиенте сводится к обычному `send` серверу |
| `broadcast(msg_type, data)` | Алиас `send` |
| `poll()` | Забрать общую очередь под локом, без блокировки — дёргать раз в кадр |
| `peek()` / `get_peers()` | Неразрушающее чтение; `get_peers() -> [{id, name}]` только на сервере |

Состояние: `is_server` / `is_client` / `is_connected`, `local_id` (сервер `0`, клиент — выданный id), `peer_count`, `bytes_sent` / `bytes_received` (только клиентская сторона). Только TCP — без UDP, шифрования и геймплейной аутентификации; частые состояния платят head-of-line блокировкой. `NetworkPlugin.poll()` и `NetworkManager.on_update()` дренируют одну очередь, поэтому насос должен быть ровно один на кадр.

См. также: Типы сообщений (таблица ретрансляции), Транспорт и протокол (обзор), NetworkManager.

Проверено по: `core/network/transp/transport.py:39-80,82-157`; `core/network/transp/server.py:29-66,97-124,126-221,223-317`; `core/network/transp/client.py:19-64,77-129,148-194`; `core/network/protocol.py:97-118`; `core/config/constants.py:20-21`.
