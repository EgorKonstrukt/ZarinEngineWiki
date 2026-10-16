# Snapshots and Interpolation (Gameplay)

How per-entity state flows: authority sides emit at capped rates, the server relays, receivers buffer timestamped frames and interpolate. `NET_SNAPSHOT = 111` is accepted and relayed by the transport but has **no built-in emitter or consumer** — it is a reserved channel for custom whole-world snapshots; the four component channels below are the working snapshot system.

## Send rates and gating

Each component accumulates `dt` and sends at most every `1.0 / max(1.0, send_rate)` seconds — and only when the value actually changed:

| Component | Default `send_rate` | Inspector range | Gate | Tick source |
|---|---|---|---|---|
| `NetworkTransform` | 20.0 | 1–60 | `pos_threshold` 0.001, `rot_threshold` 0.5°, first send always | `on_update` |
| `NetworkRigidbody` | 20.0 | 1–60 | `velocity_threshold` 0.01 on vel + angular | `on_fixed_update` |
| `NetworkAnimator` | 10.0 | 1–30 | clip/time/speed/playing/params/trigger diff | `on_update` |
| `NetworkVariables` | 10.0 | 1–30 | dirty flag + `sync_on_change` immediate send | `on_update` |

(`network_transform.py:227-234`, `network_rigidbody.py:160-168`, `network_animator.py:131-165`, `network_variables.py:156-168`.) Payloads carry `{net_id, t, seq}` plus channel keys (`p`/`r`/`s`, `v`/`w`, animator state, `vars`). `NetworkManager.tick_rate` (default 20.0, range 1–60) is an inspector field on the session component; per-component `send_rate` is what actually paces traffic.

## Owner-driven vs authoritative

Every channel has a `SERVER` / `OWNER` authority enum defaulting to `SERVER` (`TransformAuthority`, `RigidbodyAuthority`, `VariableAuthority`, animator equivalent). `_can_send` returns `is_server` under `SERVER` and `ident.is_owner` under `OWNER`; `apply_snapshot` ignores echoes — owner's own frames under `OWNER` authority, and anything received on the server under `SERVER` authority — and drops stale `seq` frames. Ownership itself moves via `NET_OWNER_CHANGE`: a client sends `{requester}`, the server applies and `broadcast`s `{net_id, owner_id}` (`netmgr/sync.py:355-379`).

## Manager dispatch

`NetworkManager.on_update` drains the transport once per frame (`poll()`) and routes by type (`netmgr/manager.py:124-181`): `NET_SPAWN` → `_handle_spawn`, `NET_SPAWN_REQUEST` → `_handle_spawn_request` (server only), `NET_DESPAWN` → `_handle_despawn`, `NET_RPC {"rpc":...}` → plugin `_dispatch_rpc` else `_handle_rpc`, `NET_OWNER_CHANGE` → `_handle_owner_change`, `NET_KICK` → `disconnect()`, `PEER_JOINED` → `_sync_new_peer` + optional auto-spawn of `player_prefab` at a random ±2 m offset, `LEAVE` → despawn that peer's owned entities. State channels resolve the entity by `net_id` and call `comp.apply_snapshot(data)`; `NET_VARIABLES` without a `NetworkVariables` component falls back to generic `setattr` per `component_key` prop map.

## Interpolation

Receivers append `{t, seq, ...}` to a bounded deque (transform 32, rigidbody 20) and render at `now - interp_delay` (transform 0.1 s, rigidbody 0.08 s), lerping between bracketing frames; with `interpolate = False` the latest frame applies directly. `teleport_threshold` (default 5.0 m) clears the buffer and snaps on jumps. Variables/animator apply latest-wins with `seq` guards instead of buffering.

```python
tr.send_rate = 30.0        # NetworkTransform: 30 Hz cap
tr.pos_threshold = 0.01   # ...but only when moved > 1 cm
tr.interpolate = True
tr.interp_delay = 0.1
rb.send_rate = 20.0        # NetworkRigidbody, paced in on_fixed_update
vars_comp.sync_on_change = True  # NetworkVariables: send immediately on set_var
```

Related: Message Types (channel ids + relay rules), RPC Reference, NetworkTransform / NetworkRigidbody / NetworkAnimator / NetworkVariables.

Verified against: `core/components/network/netmgr/manager.py:124-222`; `core/components/network/netmgr/sync.py:200-379`; `core/components/network/network_transform.py:17-64,72-145,147-199,221-260`; `core/components/network/network_rigidbody.py:16-55,69-154,156-200`; `core/components/network/network_animator.py:131-182`; `core/components/network/network_variables.py:14-46,54-131,132-168`; `core/network/transp/server.py:232-263`; `core/network/protocol.py:80-91`.
