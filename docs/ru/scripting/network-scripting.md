# Сеть из кода

Мультиплеер из игрового кода: RPC, подписки на сообщения, спавн/деспавн префабов, авторити и частоты отправки.

## Понятия (как определяет код)

- Компонент `NetworkManager`: управление транспортом + спавн/деспавн + отправка RPC. Синглтон через `NetworkManager.get()`.
- `NetworkIdentity`: per-сущность `net_id`/`owner_id`/`prefab_id` + `AuthorityMode`: `SERVER`, `OWNER`, `SHARED`.
- Синк-компоненты: `NetworkTransform` (авторити `SERVER`/`OWNER`, `send_rate` по умолчанию `20.0`), `NetworkVariables` (авторити + `send_rate` по умолчанию `10.0`), плюс `NetworkRigidbody`/`NetworkAnimator` (тот же паттерн `send_rate`). У самого `NetworkManager` — **`tick_rate`** (по умолчанию `20.0`), а не `send_rate`.
- Поиск RPC-хендлера на принимающей сущности: метод с декоратором `@server_rpc`/`@client_rpc`/`@target_rpc` вызывается как `fn(args_dict)` → иначе `handle_rpc(method, args, sender)` → иначе `rpc_<method>(**args)`.
- Шина плагина: `NetworkPlugin.on_message(msg_type, handler)` получает `NetworkMessage(msg_type, payload, sender_id)`; `"*"` подписывает на всё.

## API NetworkManager

| Сигнатура | Поведение |
|---|---|
| `NetworkManager.get() -> NetworkManager \| None` | Кэшированный `_instance`, иначе поиск компонента по активной сцене. |
| `mgr.host(port=None, max_players=None) -> bool` | Хостинг на `0.0.0.0`; сбрасывает таблицы net-id, регистрирует существующие identity. |
| `mgr.connect(address=None, port=None, name="Player") -> bool` | Подключение клиентом; чистит таблицы. |
| `mgr.disconnect()` | Разрыв транспорта, очистка таблиц. |
| `mgr.is_server` / `is_client` / `is_connected` / `local_id` / `peer_count` | Read-only свойства поверх транспорта. |
| `mgr.send_rpc(net_id, method, args=None, target=None)` | Сервер без target — broadcast; сервер + target — `send_to(target)`; клиент — обычный `send`. Пейлоад: `{net_id, method, args, t}`. |
| `mgr.spawn_entity(entity, owner_id=None) -> int` | Сервер: назначает net id, штампует identity, шлёт `NET_SPAWN`. Клиент: шлёт `NET_SPAWN_REQUEST`, возвращает `-1`. |
| `mgr.spawn_prefab(prefab_path, pos=None, rot=None, owner_id=None)` | Сервер: `Prefab.load` для путей `.zpep`, ставит `Transform` из `pos` (`Vec3`/список) и `rot` (`Quat`/список), штампует `prefab_id`, бродкастит. Клиент: пересылает запрос серверу. Возвращает сущность (сервер) или `None` (клиент). |
| `mgr.despawn(net_id)` / `mgr.despawn_entity(entity)` | Сервер бродкастит `NET_DESPAWN` и удаляет; клиент пересылает и выходит. Чужой клиент сущности других владельцев деспавнить не может. |

Хелперы identity: `ident.is_spawned` (`net_id >= 0`), `ident.is_owner`, `ident.has_authority` (SHARED ⇒ всегда; OWNER ⇒ `is_owner`; SERVER ⇒ `is_server` транспорта), `transfer_ownership(new_owner)` (только сервер), `request_ownership()` (клиент→сервер).

`NetworkVariables`: `set_var(name, value)` (метит dirty, шлёт при изменении), `get_var(name, default)`, `set_vars(dict)`, `has_var`, `remove_var`, `all_vars()`; flush в `on_update` ограничен `sync_on_change` + `send_rate`.

## Пример синхронизации счёта (полный, рабочий)

Серверный авторити: клиенты просят прибавку через RPC, сервер обновляет запись `NetworkVariables`, которая реплицируется всем:

```python
from core.network.rpc import server_rpc


class ScoreSync:
    score_key: str = "score"

    def on_start(self):
        from core.components.network.netmgr.manager import NetworkManager
        self.mgr = NetworkManager.get()
        ident = self._entity.get_component_by_name("NetworkIdentity")
        self.net_id = ident.net_id if ident is not None else -1
        self.vars = self._entity.get_component_by_name("NetworkVariables")
        # плюс подписка на лёгкое сообщение шины:
        try:
            from core.engine.eng import Engine
            plug = Engine.instance().plugin_manager.get_plugin("NetworkPlugin")
            if plug is not None:
                plug.on_message("score_flash", self._on_flash)
        except Exception:
            pass

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.P):
            if self.mgr is not None and self.net_id >= 0:
                self.mgr.send_rpc(self.net_id, "add_point", {"by": 1})

    @server_rpc
    def add_point(self, args):
        if self.vars is None:
            return
        cur = int(self.vars.get_var(self.score_key, 0))
        self.vars.set_var(self.score_key, cur + int(args.get("by", 1)))

    def _on_flash(self, msg):
        Logger.info(f"flash from {msg.sender_id}: {msg.payload}")

    def current_score(self):
        return int(self.vars.get_var(self.score_key, 0)) if self.vars is not None else 0
```

Сетап: на сущности `NetworkIdentity` (авторити `SERVER`) + `NetworkVariables` + этот скрипт. `send_rpc` маршрутизирует по роли автоматически; `@server_rpc` выполняется на серверной копии через `invoke_rpc`.

Спавн сетевого префаба из игры:

```python
class NetSpawner:
    prefab_path: str = "assets/prefabs/player.zpep"

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.N) and self.mgr.is_server:
            ent = self.mgr.spawn_prefab(self.prefab_path, pos=Vec3(0.0, 1.0, 0.0))
            Logger.info(f"spawned: {ent.name if ent else None}")
```

## Связанные страницы

- `entity-api.md` — заспавненные сущности — обычные сущности с прицепленной identity.
- `prefab-scripting.md` — локальный (несетевой) спавн префабов.
- `timers-coroutines.md` — троттлите `send_rpc` кулдауном, а не шлите каждый кадр.

## Проверено по коду

- `core/components/network/netmgr/manager.py:22-65` (поля + `get()`), `:116-122` (awake/destroy синглтона).
- `core/components/network/netmgr/transport.py:14-52` (свойства ролей), `:54-101` (host/connect/disconnect), `:103-145` (despawn), `:147-161` (`send_rpc(net_id, method, args, target)`).
- `core/components/network/netmgr/sync.py:14-56` (`spawn_entity`), `:58-198` (`spawn_prefab(path, pos, rot, owner)`).
- `core/network/rpc.py:15-30` (декораторы), `:33-69` (цепочка `invoke_rpc`), `:72-106` (направленные хелперы).
- `core/components/network/network_identity.py:13-46` (поля + `AuthorityMode`), `:60-107` (авторити/владение).
- `core/components/network/network_transform.py:25-51` (`send_rate = 20.0`), `network_variables.py:36-39` (`send_rate = 10.0`), `:71-97` (акцессоры vars), `:156-167` (flush по рейту).
- `plugins/network_plugin.py:13-17` (`NetworkMessage`), `:94-115` (`send`/`broadcast`/`on_message`), `:118+` (`_dispatch_rpc`, wildcard `"*"`).
