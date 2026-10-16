# Physics Scene

A ball drops onto the ground, gets pushed with keys, and counts impacts. Four steps, all Inspector names are the real component/field names.

## 1. Ground entity

- `Hierarchy` add button (`Create Entity`) → rename to `Ground`.
- `Inspector → Add Component` → add `BoxCollider`. In the Inspector set `Size` to `Vec3(10.0, 1.0, 10.0)`; leave `Center` at zero and `Is Trigger` off — a non-trigger collider blocks and reports contacts.
- No `Rigidbody` on the ground: without one it acts as a static obstacle.

## 2. Ball entity with Rigidbody

- Create entity `Ball`, add `Rigidbody` then `SphereCollider` (`Add Component` each).
- `Rigidbody` Inspector: `Mass = 1.0`, `Drag = 0.0`, `Use Gravity` on, `Is Kinematic` off. These are the real field names (`mass`, `drag`, `angular_drag`, `use_gravity`, `is_kinematic`); per-axis freeze flags exist for locking movement later.
- Move `Ball` up (`Transform` position `y = 5.0`) so the fall is visible.

## 3. Scripts: push in fixed step, count hits

- `Project → Create → Python Script` → `ball.py`; attach via `Add Component → Scripts` on `Ball`.

```python
class Ball:
    push: float = 8.0
    hits: int = 0

    def on_awake(self):
        self.body = self._entity.get_component_by_name("Rigidbody")

    def on_fixed_update(self, dt):
        if not self.body:
            return
        if Input.GetKey(KeyCode.W):
            self.body.add_force(Vec3(0.0, 0.0, self.push))
        if Input.GetKey(KeyCode.S):
            self.body.add_force(Vec3(0.0, 0.0, -self.push))
        if Input.GetKeyDown(KeyCode.SPACE):
            self.body.add_impulse(Vec3(0.0, 5.0, 0.0))

    def on_collision_enter(self, other_id, force):
        self.hits = self.hits + 1
        Logger.info(f"hit {other_id} force {force} total {self.hits}")
```

Rules from the engine: forces and impulses belong in `on_fixed_update(dt)` (stable `0.02` default step); `add_force` accumulates until the solver consumes it, `add_impulse` applies a mass-scaled instant kick (`impulse * (1/mass)`); collision arity is measured from the signature — `(other_id)` alone or `(other_id, force)` both work. Resolve the other entity from `other_id` only when needed; counting by id is cheapest.

## 4. Play and verify

- Press `Play`: the ball falls, lands on the ground collider, WASD pushes it, `Space` kicks it upward, each new contact logs a `hit ... force ...` line with the other entity id.
- `Is Kinematic` on the ball freezes solver motion (script motion still applies); `Is Trigger` on a collider reports contacts without blocking. Both toggles are live Inspector checkboxes.
- Failure modes: ball falls through → the collider is missing or `Is Trigger` is on with no contact handling; no motion from keys → script attached to the wrong entity or `body` is `None` (check the `Script` field points at `ball.py`).

Next: GUI Menu, Prefabs, Audio, Build — widgets, `.zpep` reuse, positional audio, running and shipping the scene.
