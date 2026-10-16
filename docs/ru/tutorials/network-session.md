# Урок: сетевая сессия

Игровая сессия на два инстанса за 7 шагов: `NetworkManager` хостит/спавнит, `NetworkIdentity` + `NetworkTransform` + `NetworkPlayer` реплицируют аватары. Дефолты — собственные дефолты компонентов (`netmgr/manager.py`, `network_identity.py`, `network_transform.py`, `network_player.py`).

Дефолты `NetworkManager`: `port = 7777`, `max_players = 16`, `server_name = "Zarin Server"`, `host_address = "127.0.0.1"`, `player_prefab = ""`, `auto_spawn_player = True`, `tick_rate = 20.0`. Дефолты `NetworkTransform`: `sync_position/rotation = True`, `sync_scale = False`, `authority = SERVER`, `send_rate = 20.0`, `interpolate = True`, `interp_delay = 0.1`, `teleport_threshold = 5.0`. `NetworkIdentity` стартует с `net_id = owner_id = -1`, `authority = SERVER`.

Как работает спавн (`manager.py:on_update`): по `PEER_JOINED`, если `is_server`, менеджер досылает пиру существующие сущности и — когда `auto_spawn_player` и задан `player_prefab` — вызывает `spawn_prefab(prefab, pos=random ±2 м, owner_id=peer)`. Транспорт: `host(port, max_players)` биндит `0.0.0.0`; `connect(address, port, name)` входит; `despawn(net_id)` удаляет везде.

## Шаги

1. **Соберите префаб игрока.** Сущность `PlayerPrefab` с `NetworkIdentity` + `NetworkTransform` (+ `NetworkPlayer`, `player_name = "Player"`). Сохраните как `assets/prefabs/player.zpep` (фильтр префабов — `*.zpep`).
2. **Добавьте менеджер.** Сущность `Net` с `NetworkManager`: `Port = 7777`, `Player Prefab = assets/prefabs/player.zpep`, `Auto Spawn Player` оставить включённым.
3. **Повесьте `net_host.py`** (ниже) на сущность `HostStarter`. Продублируйте запуск проекта или откройте второй инстанс редактора с тем же проектом.
4. **Хост:** Play первого инстанса — `on_start` вызывает `host()` → в логе `NetworkManager hosting on 7777 max_players=16`.
5. **Вход:** Play второго инстанса с `net_client.py` — `connect("127.0.0.1", 7777)`; хост автоспавнит аватар (`PEER_JOINED` → `spawn_prefab`).
6. **Двигайте** аватар владельца: `NetworkTransform` шлёт на `send_rate` при выходе за `pos_threshold` (0.001) / `rot_threshold` (0.5°); удалённые копии интерполируются с `interp_delay`.
7. **Проверьте:** на хосте `peer_count = 1`; в обоих вьюпортах два аватара; остановка клиента отрабатывает путь `LEAVE` и деспавнит его сущности.

## Полный код

```python
# net_host.py -- вешается в хостящем инстансе
class NetHost:
    port: int = 7777

    def on_start(self):
        from core.components.network.netmgr.manager import NetworkManager
        mgr = NetworkManager.get()
        if mgr is not None and mgr.host(self.port):
            Logger.info(f"hosting, local_id={mgr.local_id}")

    def on_update(self, dt):
        from core.components.network.netmgr.manager import NetworkManager
        mgr = NetworkManager.get()
        if mgr is not None and Input.GetKeyDown(KeyCode.H):
            Logger.info(f"server={mgr.is_server} peers={mgr.peer_count}")
```

```python
# net_client.py -- вешается во входящем инстансе
class NetClient:
    address: str = "127.0.0.1"
    port: int = 7777

    def on_start(self):
        from core.components.network.netmgr.manager import NetworkManager
        mgr = NetworkManager.get()
        if mgr is not None and mgr.connect(self.address, self.port):
            Logger.info(f"connected to {self.address}:{self.port}")

    def on_update(self, dt):
        from core.components.network.netmgr.manager import NetworkManager
        mgr = NetworkManager.get()
        if mgr is not None and Input.GetKeyDown(KeyCode.H):
            Logger.info(f"connected={mgr.is_connected} peers={mgr.peer_count}")
```

`NetworkManager.get()` находит менеджер сцены; `is_server / is_client / is_connected / local_id / peer_count` — живые свойства транспорта (`netmgr/transport.py`). Владение: `NetworkIdentity.transfer_ownership(id)` (только сервер) / `request_ownership()` (клиент), `has_authority` следует `AuthorityMode`.

## Проверено по коду

`core/components/network/netmgr/manager.py`, `core/components/network/netmgr/transport.py`, `core/components/network/netmgr/sync.py`, `core/components/network/network_identity.py`, `core/components/network/network_transform.py`, `core/components/network/network_player.py`, `plugins/network_plugin.py`.
