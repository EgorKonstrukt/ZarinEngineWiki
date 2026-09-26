# Motor

Constant drive for conveyors, wheels, fans, spinning traps, elevators and servo doors — steady motion without scripting velocity every frame.

| Inspector group / field | Meaning |
|---|---|
| Connected Entity | Driven body reference |
| Motor Type | Rotation or translation drive |
| Mode | Velocity cruise or position servo |
| Axis / Anchor | Drive direction and pivot |
| Drive: Target Velocity | Cruise speed |
| Drive: Motor Force / Motor Torque | Strength caps (stall behavior) |
| Limits: Limit Low / Limit High | Travel bounds for servo mode |
| Free Spin | Ignore limits entirely |

Mode guide: velocity mode with high force reads as a conveyor or fan (never stalls); position mode with limits reads as a servo door or elevator (drives to the bound and holds); free spin wheels ignore limits and run on target velocity.

Force caps define stall: a cap below the load stalls under weight — correct for garage doors blocked by crates; an infinite-feeling cap plows through everything — correct for crushers.

Patterns: conveyor — velocity motor along the belt axis on a kinematic body; drawbridge — position servo with limits 0…π/2; windmill — free-spin rotation with modest torque; crusher — translation servo with high force and slow velocity.

Troubleshooting: never moves → connected entity missing or kinematic chain broken; overshoots bounds → force too high for the servo, lower cap or raise damping via joint; vibrates at bound → limit fight, widen the dead zone between limits.

Related: Joint (passive links), Rigidbody (driven bodies), CharacterController (riding platforms).
