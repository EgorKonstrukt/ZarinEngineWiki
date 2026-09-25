# Motor

Constant drive for conveyors, wheels, fans and spinning traps.

| Inspector group / field | Meaning |
|---|---|
| Connected Entity | Driven body reference |
| Motor Type | Rotation or translation drive |
| Mode | Velocity or position servo |
| Axis / Anchor | Drive direction and pivot |
| Drive: Target Velocity | Cruise speed |
| Drive: Motor Force / Motor Torque | Strength caps |
| Limits: Limit Low / Limit High | Travel bounds |
| Free Spin | Ignore limits |

A velocity-mode motor with high force acts like a conveyor; position mode with limits acts like a servo door.

Related: Joint, Rigidbody, CharacterController (player-driven platforms).
