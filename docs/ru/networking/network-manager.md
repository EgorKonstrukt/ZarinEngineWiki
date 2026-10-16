# NetworkManager

Компонент сессии: один на сцену, владеет хостингом, входом, спавнами игроков, деспавнами и RPC-веером на уровне сущностей.

| Группа / поле инспектора | Смысл |
|---|---|
| Network Manager: Port | Порт listen/connect, по умолчанию 7777 |
| Max Players | Кап комнаты |
| Server Name | Имя лобби |
| Host Address | Последний/пресетный адрес входа |
| Player Prefab | Спавнится на подключение |
| Auto Spawn Player | Спавн на вход без скрипта |
| Tick Rate | Синхро-тиков в секунду |
| Show Debug | Оверлей состояния сессии |

Lifecycle API: `host()`, `connect()`, `disconnect()` (зеркало плагина в скоупе сцены через `NetworkManagerTransportMixin`); `on_awake` регистрирует инстанс и существующие identity, `on_update` опрашивает общую очередь `Transport` и диспатчит по `MessageType`, `on_destroy` лишь снимает синглтон (не отключается — вызывайте `disconnect()` явно).

Геймплейный API (сверен в `netmgr/transport.py` + `netmgr/sync.py`):

- `spawn_prefab(prefab_path, pos=None, rot=None, owner_id=None)` / `spawn_entity(entity, owner_id=None)` — авторитетный контроль спавна; заспавненные несут NetworkIdentity с prefab id, и ремоуты инстанцируют тот же префаб.
- `despawn(net_id: int)` / `despawn_entity(entity: Entity)` — авторитетный деспавн по net id (сервер рассылает `NET_DESPAWN`; запрос клиента идёт на сервер).
- `send_rpc(net_id: int, method: str, args: dict | None = None, target: int | None = None)` — удалённый вызов уровня компонентов (`NET_RPC {"net_id", "method", "args", "target?"}`); сервер может unicast через `send_to(target)`, иначе broadcast.

Установка: ровно один активный менеджер на сцену (два дерутся за транспорт). Tick rate меняет полосу на свежесть — 10–20 Гц лобби и стратегии, 30–60 шутеры; пороги sync-компонентов важнее сырых тиков.

Диагностика: двойные спавны — два менеджера или автоспавн плюс ручной; mismatch деспавна — деспавните по id, никогда по локальной ссылке; RPC молчит — хендлер зарегистрирован после старта `poll` или опечатка имени.

Связанное: сетевой плагин (обвязка транспорта), NetworkIdentity (id заспавненных), NetworkSpawn (пулы префабов), NetworkPlayer (данные пиров).
