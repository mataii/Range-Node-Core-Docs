# Player Jump

**Type:** Exec (In → Out)  
**Category:** PLAYER

Applies a jump to the player object when the key configured in **Player Input** is pressed, provided the object is on the ground. Supports double jump and both Range physics modes.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Physics | Enum | `Character` | Object physics mode |
| Jump Force | Float | `7.0` | Jump force / velocity |
| Jump Frames | Int (1–60) | `10` | Frames of upward movement (Character mode only) |
| Max Jumps | Int (1–10) | `1` | 1 = normal jump, 2 = double jump, etc. |
| Ground Dist | Float | `0.6` | Downward raycast distance for ground detection. Set to: `object_height / 2 + 0.1` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Physics modes

### Character
Applies vertical `applyMovement` for `Jump Frames` frames:

```python
self.own.applyMovement((0.0, 0.0, jump_force * dt), False)
```

Each frame of the timer applies an upward impulse in world space.

### Dynamic
Directly assigns `localLinearVelocity.z` on the jump frame:

```python
vel = self.own.localLinearVelocity.copy()
vel.z = jump_force
self.own.localLinearVelocity = vel
```

## Ground detection

The node casts a downward raycast from `worldPosition` to a distance of `Ground Dist`. If the ray hits nothing, the `_pj_in_air` flag activates. When the object touches ground again, available jumps are restored.

## Published state (`_pj_*`)

| Variable | Type | Description |
|----------|------|-------------|
| `_pj_in_air` | bool | `True` while the object is not touching the ground |
| `_pj_timer` | int | Remaining frames of upward impulse (Character only) |
| `_pj_jumps_left` | int | Jumps remaining before needing to touch the ground |

These values are read by **Player Movement** (air control) and **Player State** (JUMPING / FALLING states).

## Ground Dist configuration

An incorrect `Ground Dist` value causes the object to fail to detect the ground or to never lose it:

| Object height | Recommended Ground Dist |
|---------------|-------------------------|
| 1.8 u (human) | 0.95–1.0 |
| 1.0 u | 0.55–0.6 |
| 0.5 u | 0.3–0.35 |

!!! warning "Requires Player Input on the same object"
    Reads `_pi_jump` to know when to jump.

## Example graph

```
[OnUpdate] → [Player Input] → [Player Jump] → [Player Movement] → ...
```

Player Jump must come **before** Player Movement in the graph so that `_pj_in_air` is available when Player Movement reads it.
