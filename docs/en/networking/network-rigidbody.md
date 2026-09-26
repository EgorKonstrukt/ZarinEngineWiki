# NetworkRigidbody

Physics-state sync: velocities travel alongside transforms so remotes extrapolate rather than chase.

| Inspector group / field | Meaning |
|---|---|
| Sync: Sync Velocity / Sync Angular | Velocity channel toggles |
| Network Rigidbody: Authority | RigidbodyAuthority — usually the simulating peer or host |
| Send Rate | Updates per second |
| Vel Threshold | Minimum velocity change worth sending |
| Interpolate / Interp Delay | Remote smoothing twin of the transform settings |

Reads in `on_fixed_update` (physics clock) and writes visually in `on_update` — sync follows the same split as simulation, so no half-step tearing.

Pairing rule: always attach alongside NetworkTransform on dynamic bodies. Transform alone makes remotes chase with position-only correction (swimming look); velocities let remotes coast ballistically between snapshots — the difference between playable and drunk driving.

Authority posture: owner-simulated (driver owns the car, host trusts) for responsiveness; host-simulated for competitive integrity (host integrates, all render). Thresholds hide idle jitter; teleport on the transform handles respawns.

Troubleshooting: orbiting remotes → velocity without position sync, pair both; explosions of energy on catch-up → interp delay too low, raise; host/client disagree on stacks → single authority only, never two writers.

Related: NetworkTransform (position twin), Rigidbody (local API), NetworkIdentity (authority source), Solver and Threading (fixed-step source).
