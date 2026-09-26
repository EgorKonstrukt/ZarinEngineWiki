# NetworkTransform

Bandwidth-thrifty transform sync: which axes travel, how often, how remotes smooth, when to snap.

| Inspector group / field | Meaning |
|---|---|
| Sync: Sync Position / Rotation / Scale | Per-channel toggles — static props sync nothing |
| Network Transform: Authority | TransformAuthority — who may write |
| Send Rate | Updates per second for this entity |
| Pos / Rot Threshold | Minimum change worth sending (dead reckoning cutoff) |
| Interpolate | Smooth remote motion between snapshots |
| Interp Delay | Buffer delay hiding jitter (higher = smoother + later) |
| Teleport Threshold | Distance snap: beyond it, snap instead of gliding |

API: `apply_snapshot(data)` — remote write path; `teleport(position)` — snap locally and remotely; motion itself is read in `on_update` from the Transform.

Tuning per archetype: players — position+rotation at 20–30 Hz, small thresholds, interpolate on with ~100 ms delay; crates — position only at 5–10 Hz, coarse thresholds; static doors — nothing until moved, then a teleport; camera-followed racers — higher rate, lower delay.

Thresholds beat raw rates: a 10 Hz stream with tight thresholds often moves less data than 30 Hz with zero thresholds on idle-heavy scenes. Teleport threshold prevents cross-map gliding on respawn — set it near the level's room size.

Troubleshooting: rubber-banding → delay too low for the jitter, raise interp delay; moonwalking (rotation lag) → rotation threshold too coarse; snap on stairs → teleport threshold below step distances, raise; frozen remotes → authority mismatch, writes rejected.

Related: NetworkIdentity (id + authority), NetworkRigidbody (physics twin), NetworkManager (tick rate ceiling).
