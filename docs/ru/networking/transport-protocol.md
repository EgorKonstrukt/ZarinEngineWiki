# Транспорт и протокол

Проводной слой под геймплейным неткодом и коллаборацией: TCP-сокеты, msgpack-фреймы, типизированные сообщения, колбэки соединений.

Транспорт (`core/network/transport.py`, синглтон через `get_transport()`):

- `host(bind, port, max_players)` / `connect(host, port, name)` / `disconnect()` — жизненный цикл сессии. Игровой порт по умолчанию 7777.
- `send(payload)` — клиент серверу; `send_to(peer_id, type, data)` — сервер одному пиру; `broadcast(type, data)` — сервер всем.
- `poll()` — забрать входящие `[(msg_type, data)]` без блокировки; дёргать каждый кадр.
- Состояние: `is_connected`, `is_server`, `local_id`, `peer_count`; колбэки `set_on_connected`, `set_on_disconnected`.
- `set_max_players(n)` капает комнату.

Протокол (`core/network/protocol.py`): `MessageType` IntEnum — каждый фрейм несёт тип плюс dict-payload. Геймплейные RPC едут как `NET_RPC` с `{"rpc": имя, "payload": {...}}`; сервер штампует id отправителя до диспатча.

Server (`server.py`): `set_password`, `set_room`, `set_max_clients`, `start`, `stop`, `update_scene_data` (источник авторитетного снапшота). Client (`client.py`): connect/disconnect/send с `set_on_auth_failed` на неверный пароль. Relay (`relay.py`): форвардящие сессии с хуками auth-failure. UPnP (`upnp.py`): авто-проброс портов, чтобы хосты за домашними роутерами принимали прямые соединения.

Модуль RPC (`rpc.py`): соглашения именования и диспатча удалённых вызовов для геймплея и моста редактора.

Заметка о фрейминге: msgpack держит пакеты мелкими и бесхемными — dict in, dict out. Держите payload плоскими и крошечными (id, позиции, enum); никогда не возите целые сцены покадрово — возите снапшоты и дельты.

Диагностика: connect refused — сервер не стартовал или порт закрыт/файрвол; auth fails — mismatch пароля/комнаты, ловится `set_on_auth_failed`; за NAT — включите UPnP или relay; молчаливые пиры — `poll()` не дёргается каждый кадр.

Связанное: сетевой плагин (геймплейная обвязка), NetworkManager (компонент сессии), коллаборация (сессии редактора), RPC в NetworkManager.send_rpc.
