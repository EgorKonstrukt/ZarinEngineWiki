# RPC-справочник

Удалённые вызовы едут как `NET_RPC = 103` в двух разных формах. Не смешивайте их: события плагина — `{"rpc", "payload"}`, вызовы компонентов — `{"net_id", "method", "args", ...}`. Сервер ставит метку `_sender` на ретранслируемые фреймы до диспатча.

## Форма A — события плагина (`NetworkPlugin`)

`send(msg_type, payload, target_id=-1)` заворачивает вызов в `{"rpc": name, "payload": {...}}` и использует `send_to`, если `target_id >= 0` на сервере, иначе `broadcast`; `broadcast(msg_type, payload)` рассылает всегда (`plugins/network_plugin.py:94-113`).

```python
plug = engine.plugin_manager.get_plugin("NetworkPlugin")
plug.on_message("chat", lambda msg: print(msg.sender_id, msg.payload))
plug.broadcast("chat", {"text": "hello"})          # всем
plug.send("ping1", {"t": 1.0}, target_id=peer_id)  # сервер -> одному пиру
plug.poll()  # каждый кадр: разбирает RPC плагина, остаток возвращает в очередь
```

Подписка — `on_message(msg_type, handler)`; обработчики получают `NetworkMessage(msg_type, payload, sender_id)`. `_dispatch_rpc` ищет обработчики по имени `rpc` с фолбэком на обработчики `"*"` (`network_plugin.py:115-142`). `handle_transport_messages` отделяет фреймы `NET_RPC {"rpc": ...}` для плагина и возвращает остаток; `poll()` забирает очередь транспорта и кладёт leftovers обратно в её начало (`network_plugin.py:144-180`).

## Форма B — вызовы компонентов (`NetworkManager` + `core/network/rpc.py`)

`NetworkManager.send_rpc(net_id, method, args=None, target=None)` строит `{"net_id", "method", "args", "t", "target?"}` и маршрутизирует: `send_to` при заданном `target` на сервере, `broadcast` на сервере либо при `target is None`, иначе `send` серверу (`netmgr/transport.py:147-161`).

```python
mgr = NetworkManager.get()
mgr.send_rpc(net_id, "take_damage", {"amount": 10})            # сервер разошлёт / клиент попросит сервер
mgr.send_rpc(net_id, "heal", {"amount": 5}, target=peer_id)    # сервер -> одному пиру

from core.ecs import Component
class Health(Component):
    @server_rpc
    def take_damage(self, args): ...
```

Хелперы в `core/network/rpc.py:72-105`: `send_server_rpc` (клиент шлёт, сервер рассылает), `send_client_rpc` (только сервер рассылает), `send_target_rpc` (сервер — `send_to`, клиент — обычный `send`). Декораторы `server_rpc` / `client_rpc` / `target_rpc` помечают метод `_rpc_type` и регистрируют по имени. `invoke_rpc(entity, method, args, sender)` сначала пробует помеченный метод (`fn(args)`, запасной вариант `fn()`), затем `handle_rpc(method, args, sender)`, затем `rpc_<method>(**args)`.

## Порядок диспатча

`NetworkManager.on_update` проверяет фреймы `NET_RPC` первыми: содержащие `"rpc"` уходят в `_dispatch_rpc` плагина, остальные — в `_handle_rpc` (`netmgr/manager.py:160-172`). `_handle_rpc` отбрасывает чужие `target`, пропускает RPC типа `client` на сервере и вызывает остальные на сущности, найденной по `net_id` (`netmgr/sync.py:323-353`).

См. также: Типы сообщений (строка NET_RPC), Снапшоты и интерполяция (диспатч `apply_snapshot`), компонент NetworkManager.

Проверено по: `plugins/network_plugin.py:94-180`; `core/components/network/netmgr/transport.py:147-161`; `core/components/network/netmgr/manager.py:124-131,160-172`; `core/components/network/netmgr/sync.py:323-353`; `core/network/rpc.py:12-106`; `core/network/transp/server.py:232-263`.
