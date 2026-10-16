# Relay and UPnP (NAT Traversal)

Two ways to connect peers that cannot reach each other directly: a WebSocket relay room, or a UPnP port mapping on the local gateway. Both serve the **collaboration** path (`CollaborationManager`); gameplay `GameServer` has neither.

## Relay (`core/network/relayp/`)

`RelayServer(host="0.0.0.0", port=8765, max_rooms=256, max_peers_per_room=32)` speaks WebSocket + msgpack (`max_size` 8 MiB, ping 20 s): `start_sync(timeout)` / `stop_sync()` run it on a daemon thread (`relayp/server.py:19-105`). Default port constant is `DEFAULT_RELAY_PORT = 8765`. The server routes typed payloads with `_route_pair` (e.g. `CURSOR` → `CURSOR_UPDATE`, `ENTITY_CREATE` → `ENTITY_CREATED`) and speaks ops `join` / `welcomed` / `fail` / `data {"op","to","t","d"}` / `peer_joined` / `peer_left` / `ping` / `pong`.

`RelayClient.connect(relay_url, room, name="User", password="", timeout=10.0)` normalizes the URL (`ws://` prepended when missing) and the room code, derives `token = sha256(room + "\x00" + password)` when a password is set, and sends a `join` hello (`relayp/client.py:78-99,175-212`). `welcomed` stores the string `peer_id` and queues `JOINED {your_id, your_color, peers, room, version}`; `fail` queues `AUTH_FAIL` and fires `on_auth_failed`; remote traffic arrives as `data` frames queued by type, joins/leaves as `PEER_JOINED` / `LEAVE`. `send(msg_type, data)` wraps into the `data` envelope (optional `target` key selects a peer) and refuses payloads over 7 MiB; `poll_messages()` drains; `wait_connected(timeout)` blocks until id + auth state resolve.

Manager wiring (`collab/manager.py:194-294`): `host_relay(relay_url, room="", ...)` generates a 6-char room code when empty and returns it; `join_relay(relay_url, room, ...)` joins as guest; `create_invite(public=True)` builds a `ZARIN1:` invite — relay mode when connected via relay, otherwise a direct invite (public + UPnP-mapped prefers the external IP); `join_invite(code, name, password="")` parses the invite (`v`, `mode`, `host`/`port` or `relay`, `room`, `locked`) and connects accordingly. Invite helpers: `make_invite` / `parse_invite`, `build_direct_invite` / `build_relay_invite`, `PROTOCOL_VERSION = 2` (`protocol.py:18-23,180-215`).

```python
mgr.host_relay("ws://relay.example.com:8765", room="", password="s3cret", name="Host")
code = mgr.create_invite()          # "ZARIN1:..." -> share it
mgr.join_invite(code, name="Guest", password="s3cret")  # other side
```

## UPnP (`core/network/upnpp/`, `collab/upnp.py`)

`UpnpMapper(lease=3600)` discovers gateways over SSDP and maps ports: `map_port(external, internal=None, protocol="TCP", description="ZarinEngine", try_alternatives=True, timeout=10.0)` tries `external … external+16`, skips in-use mappings (fault 718), and returns `{ok, external, internal, protocol, external_ip, error}`; `unmap_port(external, protocol)` / `unmap_all()` remove mappings; a background thread renews leases at `lease/2` (`upnpp/mapper.py:175-339`).

The collaboration mixin automates this: `start_server` maps the port when bound to `0.0.0.0` and `settings.upnp_enabled` (default `True`, lease 3600); status is a dict with `state` in `idle` / `discovering` / `mapped` / `failed` / `disabled` plus `external_ip` / `external_port` / `gateway` / `error` (`collab/upnp.py:108-145`). Extras: `refresh_upnp()`, `map_extra_port(port)`, `unmap_extra_port(port)`; stopping clears mappings.

## When to use which

Direct connect works on LAN or with a reachable public IP (`connect(host, port, ...)`). Behind NAT without router access, use the relay (`host_relay` / `join_relay`) — no inbound ports needed, at the cost of routing all traffic through the relay host. When you control the router but lack a static IP, enable UPnP and share `create_invite(public=True)` so guests get the mapped external endpoint.

Related: Collaboration Protocol (sessions), Message Types (invite/auth types), Transport Internals (direct path).

Verified against: `core/network/relayp/server.py:19-105`; `core/network/relayp/relay.py:24-41`; `core/network/relayp/client.py:78-110,175-212,293-324`; `core/network/collab/manager.py:109-157,194-294`; `core/network/collab/upnp.py:15-39,108-190`; `core/network/upnpp/mapper.py:175-339`; `core/network/protocol.py:18-23,121-144,166-215`; `core/network/collab/models.py:35-52`.
