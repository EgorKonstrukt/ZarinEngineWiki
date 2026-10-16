# Релей и UPnP (проход NAT)

Два способа соединить пиров без прямой достижимости: WebSocket-релей с комнатами либо UPnP-маппинг порта на домашнем роутере. Оба обслуживают путь **коллаборации** (`CollaborationManager`); у геймплейного `GameServer` их нет.

## Релей (`core/network/relayp/`)

`RelayServer(host="0.0.0.0", port=8765, max_rooms=256, max_peers_per_room=32)` говорит WebSocket + msgpack (`max_size` 8 МиБ, ping 20 с): `start_sync(timeout)` / `stop_sync()` крутят его в daemon-потоке (`relayp/server.py:19-105`). Константа порта по умолчанию — `DEFAULT_RELAY_PORT = 8765`. Сервер маршрутизирует типизированные данные через `_route_pair` (напр. `CURSOR` → `CURSOR_UPDATE`, `ENTITY_CREATE` → `ENTITY_CREATED`) и говорит опкодами `join` / `welcomed` / `fail` / `data {"op","to","t","d"}` / `peer_joined` / `peer_left` / `ping` / `pong`.

`RelayClient.connect(relay_url, room, name="User", password="", timeout=10.0)` нормализует URL (без схемы подставляется `ws://`) и код комнаты, при пароле считает `token = sha256(room + "\x00" + password)` и шлёт hello-`join` (`relayp/client.py:78-99,175-212`). `welcomed` сохраняет строковый `peer_id` и кладёт в очередь `JOINED {your_id, your_color, peers, room, version}`; `fail` кладёт `AUTH_FAIL` и дёргает `on_auth_failed`; чужой трафик приходит `data`-фреймами в очередь по типам, входы/выходы — как `PEER_JOINED` / `LEAVE`. `send(msg_type, data)` заворачивает в конверт `data` (ключ `target` выбирает пира) и режет пакеты больше 7 МиБ; `poll_messages()` забирает очередь; `wait_connected(timeout)` ждёт связку id + статус авторизации.

Обвязка менеджера (`collab/manager.py:194-294`): `host_relay(relay_url, room="", ...)` при пустой комнате генерирует 6-символьный код и возвращает его; `join_relay(relay_url, room, ...)` входит гостем; `create_invite(public=True)` строит инвайт `ZARIN1:` — в relay-режиме relay-вариант, иначе прямой (публичный + смапленный UPnP подставит внешний IP); `join_invite(code, name, password="")` парсит инвайт (`v`, `mode`, `host`/`port` либо `relay`, `room`, `locked`) и подключается по режиму. Хелперы: `make_invite` / `parse_invite`, `build_direct_invite` / `build_relay_invite`, `PROTOCOL_VERSION = 2` (`protocol.py:18-23,180-215`).

```python
mgr.host_relay("ws://relay.example.com:8765", room="", password="s3cret", name="Host")
code = mgr.create_invite()          # "ZARIN1:..." -> отправить гостю
mgr.join_invite(code, name="Guest", password="s3cret")  # вторая сторона
```

## UPnP (`core/network/upnpp/`, `collab/upnp.py`)

`UpnpMapper(lease=3600)` ищет шлюзы через SSDP и маппит порты: `map_port(external, internal=None, protocol="TCP", description="ZarinEngine", try_alternatives=True, timeout=10.0)` перебирает `external … external+16`, пропускает занятые маппинги (fault 718) и возвращает `{ok, external, internal, protocol, external_ip, error}`; `unmap_port(external, protocol)` / `unmap_all()` снимают маппинги; фоновый поток продлевает лиз за `lease/2` (`upnpp/mapper.py:175-339`).

Миксин коллаборации автоматизирует это: `start_server` маппит порт при бинде на `0.0.0.0` и включённом `settings.upnp_enabled` (по умолчанию `True`, лиз 3600); статус — словарь с `state` из `idle` / `discovering` / `mapped` / `failed` / `disabled` плюс `external_ip` / `external_port` / `gateway` / `error` (`collab/upnp.py:108-145`). Дополнительно: `refresh_upnp()`, `map_extra_port(port)`, `unmap_extra_port(port)`; при остановке маппинги снимаются.

## Когда что использовать

Прямое подключение работает в LAN или при доступном белом IP (`connect(host, port, ...)`). За NAT без доступа к роутеру — релей (`host_relay` / `join_relay`): входящие порты не нужны, ценой гонки всего трафика через хост релея. Если роутер свой, но нет статического IP — включите UPnP и раздайте `create_invite(public=True)`, гости получат смапленный внешний адрес.

См. также: Протокол коллаборации (сессии), Типы сообщений (инвайты/авторизация), Внутреннее устройство транспорта (прямой путь).

Проверено по: `core/network/relayp/server.py:19-105`; `core/network/relayp/relay.py:24-41`; `core/network/relayp/client.py:78-110,175-212,293-324`; `core/network/collab/manager.py:109-157,194-294`; `core/network/collab/upnp.py:15-39,108-190`; `core/network/upnpp/mapper.py:175-339`; `core/network/protocol.py:18-23,121-144,166-215`; `core/network/collab/models.py:35-52`.
