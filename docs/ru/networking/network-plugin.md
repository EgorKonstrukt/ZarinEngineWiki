# Сетевой плагин

Системный плагин (`SYSTEM = True`), связывающий геймплей с транспортом: host, connect, хендлеры сообщений и покадровый poll.

```python
plug = engine.plugins.get("NetworkPlugin")
plug.host(port=7777, max_players=16)
plug.connect("192.168.1.10", 7777)
plug.on_message("chat", handle_chat)
plug.broadcast("chat", {"text": "hello"})
plug.poll()
```

API, сверено:

- `host(port=7777, max_players=16)` — старт сервера на всех интерфейсах; логирует итог, возвращает bool.
- `connect(host, port=7777)` — вход как `"Player"`; возвращает bool.
- `disconnect()` — выход; также при shutdown плагина.
- `send(msg_type, payload, target_id=-1)` — серверный таргетный `send_to` при id ≥ 0, иначе broadcast; всё едет как `NET_RPC`.
- `broadcast(msg_type, payload)` — всем пирам.
- `on_message(msg_type, handler)` — подписка; хендлер получает `NetworkMessage(msg_type, payload, sender_id)`. Wildcard `"*"` получает всё необработанное.
- `poll()` — осушить транспорт, раздать `NET_RPC` по хендлерам (исключения хендлеров логируются, не пробрасываются), остальное вернуть в очередь.
- Состояние: `is_connected`, `is_server`, `local_id`, `peer_count`.

Паттерн хендлеров: один хендлер на имя сообщения, валидация payload до использования, wildcard — логгер неизвестных. Дёргайте `poll()` раз в кадр из игрового кода или компонента-менеджера — неосушённые очереди читаются как мёртвые пиры.

```python
def handle_chat(msg):
    line = str(msg.payload.get("text", ""))
    Logger.info("chat from " + str(msg.sender_id) + ": " + line)

plug.on_message("chat", handle_chat)
plug.on_message("*", log_unknown)
```

Связанное: транспорт и протокол, NetworkManager (сессии уровня компонентов), NetworkVariables (близнец RPC для состояния).
