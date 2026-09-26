# NetworkAnimator

Animation-state sync: which clip, at what time, speed and playing flag — plus gameplay parameters.

| Inspector group / field | Meaning |
|---|---|
| Sync: Sync Clip / Time / Speed / Playing | Channel toggles — idle loops need clip only |
| Network Animator: Authority | AnimatorAuthority |
| Send Rate | Updates per second |
| Sync Params | Named parameter set (bools, floats, triggers by snapshot) |

API: `apply_snapshot(data)` remote write; `trigger(name)`, `set_float(name, value)`, `set_bool(name, value)` — local calls that replicate to remotes.

Bandwidth wisdom: sync clip + playing at low rate for ambient characters; add time sync only where foot-sync matters (dance, marching); params carry gameplay truth (aim blends, hurt flags) while clip names carry visuals. Triggers are fire-and-forget — the snapshot carries them once, so trigger during a send tick or force a send.

Fighting-game note: time sync at full rate is expensive; prefer deterministic playback (same start event + same clock) and sync only triggers and params.

Troubleshooting: T-pose on remotes → clip name mismatch (different asset names per build); desynced dance → time channel off; stuck trigger → sent between ticks and never snapshot, force on trigger.

Related: NetworkIdentity, Animation Clips and Animator, NetworkVariables (params twin).
