# Снапшоты и интерполяция (геймплей)

Как течёт состояние сущностей: владеющая сторона шлёт с ограничением частоты, сервер ретранслирует, получатели складывают фреймы с метками времени в буфер и интерполируют. `NET_SNAPSHOT = 111` транспорт принимает и ретранслирует, но **встроенных отправителей и получателей у него нет** — это зарезервированный канал под пользовательские снимки всего мира; рабочей системой снимков являются четыре компонентных канала ниже.

## Частоты отправки и фильтры

Каждый компонент копит `dt` и шлёт не чаще `1.0 / max(1.0, send_rate)` секунд — и только при реальном изменении значения:

| Компонент | `send_rate` по умолчанию | Диапазон инспектора | Фильтр | Источник тиков |
|---|---|---|---|---|
| `NetworkTransform` | 20.0 | 1–60 | `pos_threshold` 0.001, `rot_threshold` 0.5°, первая отправка всегда | `on_update` |
| `NetworkRigidbody` | 20.0 | 1–60 | `velocity_threshold` 0.01 по vel + angular | `on_fixed_update` |
| `NetworkAnimator` | 10.0 | 1–30 | diff клипа/времени/скорости/playing/параметров/триггера | `on_update` |
| `NetworkVariables` | 10.0 | 1–30 | dirty-флаг + немедленная отправка при `sync_on_change` | `on_update` |

(`network_transform.py:227-234`, `network_rigidbody.py:160-168`, `network_animator.py:131-165`, `network_variables.py:156-168`.) Пакеты несут `{net_id, t, seq}` плюс ключи канала (`p`/`r`/`s`, `v`/`w`, состояние аниматора, `vars`). `NetworkManager.tick_rate` (по умолчанию 20.0, диапазон 1–60) — поле инспектора компонента сессии; реальный темп задаёт именно `send_rate` компонентов.

## Владелец или авторитет сервера

У каждого канала есть авторитет `SERVER` / `OWNER`, по умолчанию `SERVER` (`TransformAuthority`, `RigidbodyAuthority`, `VariableAuthority` и аналог у аниматора). `_can_send` возвращает `is_server` при `SERVER` и `ident.is_owner` при `OWNER`; `apply_snapshot` игнорирует эхо — собственные фреймы владельца при авторитете `OWNER` и всё пришедшее на сервере при авторитете `SERVER` — и отбрасывает протухшие `seq`. Само владение двигается через `NET_OWNER_CHANGE`: клиент шлёт `{requester}`, сервер применяет и делает `broadcast {net_id, owner_id}` (`netmgr/sync.py:355-379`).

## Диспатч менеджера

`NetworkManager.on_update` раз в кадр забирает транспорт (`poll()`) и разбирает по типам (`netmgr/manager.py:124-181`): `NET_SPAWN` → `_handle_spawn`, `NET_SPAWN_REQUEST` → `_handle_spawn_request` (только сервер), `NET_DESPAWN` → `_handle_despawn`, `NET_RPC {"rpc":...}` → `_dispatch_rpc` плагина, иначе `_handle_rpc`, `NET_OWNER_CHANGE` → `_handle_owner_change`, `NET_KICK` → `disconnect()`, `PEER_JOINED` → `_sync_new_peer` + опциональный автоспавн `player_prefab` со случайным смещением ±2 м, `LEAVE` → деспавн сущностей этого пира. Каналы состояния находят сущность по `net_id` и зовут `comp.apply_snapshot(data)`; `NET_VARIABLES` без компонента `NetworkVariables` разбирается общим `setattr`-фолбэком по карте `component_key`.

## Интерполяция

Получатели складывают `{t, seq, ...}` в ограниченный дек (трансформ 32, rigidbody 20) и рисуют момент `now - interp_delay` (трансформ 0.1 с, rigidbody 0.08 с), интерполируя между соседними фреймами; при `interpolate = False` применяется сразу последний фрейм. `teleport_threshold` (по умолчанию 5.0 м) чистит буфер и телепортирует при прыжках. Переменные/аниматор вместо буфера применяют latest-wins с `seq`-защитой.

```python
tr.send_rate = 30.0        # NetworkTransform: потолок 30 Гц
tr.pos_threshold = 0.01   # ...но только при сдвиге > 1 см
tr.interpolate = True
tr.interp_delay = 0.1
rb.send_rate = 20.0        # NetworkRigidbody, тикает в on_fixed_update
vars_comp.sync_on_change = True  # NetworkVariables: отправлять сразу в set_var
```

См. также: Типы сообщений (id каналов + правила ретрансляции), RPC-справочник, NetworkTransform / NetworkRigidbody / NetworkAnimator / NetworkVariables.

Проверено по: `core/components/network/netmgr/manager.py:124-222`; `core/components/network/netmgr/sync.py:200-379`; `core/components/network/network_transform.py:17-64,72-145,147-199,221-260`; `core/components/network/network_rigidbody.py:16-55,69-154,156-200`; `core/components/network/network_animator.py:131-182`; `core/components/network/network_variables.py:14-46,54-131,132-168`; `core/network/transp/server.py:232-263`; `core/network/protocol.py:80-91`.
