# Сетевой плагин

Системный плагин (`SYSTEM = True`), связывающий геймплей с реальным транспортом: host, connect, хендлеры сообщений и покадровый poll поверх `get_transport()` из `core.network.transp` (`GameServer` / `GameClient` поверх TCP, msgpack-фрейминг `MessageType`).

```python
plug = engine.plugins.get("NetworkPlugin")
plug.host(port=7777, max_players=16)
plug.connect("192.168.1.10", 7777)
plug.on_message("chat", handle_chat)
plug.broadcast("chat", {"text": "hello"})
plug.poll()
```

API, сверено:

| API | Сигнатура / смысл |
|---|---|
| `host` | `host(port=7777, max_players=16) -> bool` — `get_transport().host("0.0.0.0", port, max_players)`, логирует итог |
| `connect` | `connect(host, port=7777) -> bool` — `get_transport().connect(host, port, "Player")`, логирует итог |
| `disconnect` | `disconnect()` — `get_transport().disconnect()`; также при shutdown плагина |
| `send` | `send(msg_type, payload, target_id=-1)` — `send_to(target_id, NET_RPC, ...)` при `target_id >= 0` и сервере, иначе `broadcast`; payload упаковывается как `{"rpc": имя, "payload": dict}` |
| `broadcast` | `broadcast(msg_type, payload)` — `broadcast(NET_RPC, {"rpc": имя, "payload": dict})` всем пирам |
| `on_message` | `on_message(msg_type, handler)` — подписка; хендлер получает `NetworkMessage(msg_type, payload, sender_id)`; `"*"` получает всё необработанное |
| `poll` | `poll()` — `get_transport().poll()`, раздача `NET_RPC {"rpc": ...}` по хендлерам (исключения хендлеров логируются, не пробрасываются), остальное возвращается в голову очереди |
| Состояние | `is_connected`, `is_server`, `local_id`, `peer_count` — прямые чтения `get_transport()` |

Использование: один хендлер на имя сообщения, валидация payload до использования, `"*"` — логгер неизвестных. Дёргайте `poll()` раз в кадр из игрового кода или компонента-менеджера — неосушённые очереди читаются как мёртвые пиры. `NetworkManager.on_update()` уже опрашивает ту же очередь `Transport` и форвардит `NET_RPC {"rpc": ...}` в этот плагин, поэтому не запускайте оба насоса вслепую.

```python
def handle_chat(msg):
    line = str(msg.payload.get("text", ""))
    Logger.info("chat from " + str(msg.sender_id) + ": " + line)

plug.on_message("chat", handle_chat)
plug.on_message("*", log_unknown)
```

Тонкие места: транспорт только TCP (без UDP/WebRTC/ENet); клиентский `send` всегда идёт серверу, точечная доставка — серверный `send_to`; без геймплейного пароля/комнаты/аутентификации; кап 4 МиБ на фрейм (`MAX_MESSAGE_SIZE`).

Связанное: транспорт и протокол, NetworkManager (сессии уровня компонентов), NetworkVariables (близнец RPC для состояния).

Сверено с: `plugins/network_plugin.py:13-17,33-62,64-84,94-116,118-180,182-188`; `core/network/transp/transport.py:82-144`; `core/network/protocol.py:80-91,97-110`.
