# NetworkVariables

Replicated key-value state for gameplay truth: health, scores, door flags, match phase, inventory counts.

| Inspector group / field | Meaning |
|---|---|
| Network Variables: Authority | VariableAuthority — who may write |
| Send Rate | Cap on update frequency |
| Reliable | Ordered guaranteed delivery vs latest-only |
| Sync On Change | Send only deltas, never full state per tick |

API: `set_var(key, value)`, `set_vars(dict)`, `remove_var(key)`, `apply_snapshot(data)`; dirty keys flush in `on_update`.

Reliable vs fast: health and scores use reliable (every change must land); crosshair-aim-style rapid values use unreliable latest-only (stale data is worse than lost data). Sync-on-change turns idle entities silent — a hundred quiet doors cost nothing.

```python
vars = self._entity.get_component_by_name("NetworkVariables")
if vars:
    vars.set_var("hp", 75)
    vars.set_vars({"ammo": 12, "shield": True})
```

Design rule: variables carry truth, RPCs carry events. Door opened (state) → variable; door slammed with a bang (moment) → RPC + variable. Reading truth from events replays history wrong on late joiners; snapshots fix them instantly.

Troubleshooting: late joiner sees defaults → variable never snapshotted, force full sync on join; oscillating values → two writers, fix authority; flood → sync-on-change off with per-tick sets.

Related: NetworkManager.send_rpc (events twin), NetworkPlayer (score patterns), NetworkIdentity (authority).
