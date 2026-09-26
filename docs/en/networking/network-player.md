# NetworkPlayer

Per-peer avatar data: name, id, team, ready flag and score — the lobby row as a component.

| Inspector group / field | Meaning |
|---|---|
| Network Player: Player Name | Display name |
| Player ID | Session peer id mirror |
| Team | Team index or name |
| Is Ready | Lobby ready flag |
| Score | Replicated score counter |

API: `set_ready(bool)`, `add_score(n)`, `set_team(t)`, `set_name(s)`, `apply_vars(data)` snapshot path, `on_awake` registration.

Lobby flow: join → spawn avatar with NetworkPlayer → client sets name/team → `set_ready(True)` → host counts readies → match starts. Score flows through `add_score` so kills, captures and assists accumulate server-side without client trust.

`Is Local Player` on the sibling NetworkIdentity gates input; NetworkPlayer carries the social data. Keep them on the same prefab root — split roots desync name from body.

Troubleshooting: all names default → set_name never called or applied before spawn finished; ready ignored → host counts a different component, wire to this one; score resets → snapshot overwrites local adds, add only via API on authority.

Related: NetworkIdentity (Is Local Player gate), NetworkManager (join/spawn flow), NetworkVariables (score replication twin).
