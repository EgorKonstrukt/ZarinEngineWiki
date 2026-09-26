# NetworkVariables

Реплицируемое key-value состояние для геймплейной правды: здоровье, счёт, флаги дверей, фаза матча, counts инвентаря.

| Группа / поле инспектора | Смысл |
|---|---|
| Network Variables: Authority | VariableAuthority — кому писать |
| Send Rate | Кап частоты обновлений |
| Reliable | Гарантированная доставка по порядку против latest-only |
| Sync On Change | Только дельты, никогда полное состояние в тик |

API: `set_var(key, value)`, `set_vars(dict)`, `remove_var(key)`, `apply_snapshot(data)`; грязные ключи флашатся в `on_update`.

Надёжно против быстро: здоровье и счёт — reliable (каждое изменение обязано дойти); быстрые вроде прицела — unreliable latest-only (несвежие данные хуже потерянных). Sync-on-change делает тихие сущности молчаливыми — сотня quiet дверей стоит ноль.

```python
vars = self._entity.get_component_by_name("NetworkVariables")
if vars:
    vars.set_var("hp", 75)
    vars.set_vars({"ammo": 12, "shield": True})
```

Правило дизайна: переменные несут правду, RPC — события. Дверь открыта (состояние) → переменная; дверь хлопнула с грохотом (момент) → RPC + переменная. Чтение правды из событий неверно реплеит историю опоздавшим; снапшоты чинят их мгновенно.

Диагностика: опоздавший видит дефолты — переменная никогда не снапшотилась, форсите полный синк на вход; осцилляция значений — два писателя, чините authority; флуд — выкл sync-on-change при сетах каждый тик.

Связанное: NetworkManager.send_rpc (близнец событий), NetworkPlayer (паттерны счёта), NetworkIdentity (authority).
