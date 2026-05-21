# Player Movement

**Type:** Exec (In → Out)  
**Category:** PLAYER

Applies movement to the player object by reading the `_pi_move_x` / `_pi_move_y` axes published by **Player Input**. Supports sprint speed, acceleration smoothing, and reduced air control.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Physics | Enum | `Character` | Physics mode: `Character` (applyMovement) or `Dynamic` (linearVelocity) |
| Speed | Float | `4.0` | Walking speed (units/s) |
| Sprint Speed | Float | `8.0` | Speed while sprinting (units/s) |
| Accel Lerp | Float (0.01–1.0) | `1.0` | Acceleration smoothing: 1 = instant, lower = smoother |
| Air Control | Float (0.0–1.0) | `0.3` | Speed multiplier while airborne |

## Sockets

| Socket | Direction | Type | Description |
|--------|-----------|------|-------------|
| In | Input | Exec | |
| Speed | Input | Data | Overrides the Speed property when connected |
| Sprint Speed | Input | Data | Overrides the Sprint Speed property when connected |
| Out | Output | Exec | |

## Physics modes

### Character
Uses `applyMovement()` in local space with delta time correction:

```python
self.own.applyMovement((vx * dt, vy * dt, 0.0), True)
```

Ideal for objects with **Character** physics type in Range.

### Dynamic
Directly assigns `localLinearVelocity.x/y`, preserving Z velocity (gravity):

```python
vel = self.own.localLinearVelocity.copy()
vel.x = vx
vel.y = vy
self.own.localLinearVelocity = vel
```

Ideal for **Dynamic** physics objects.

## How sprint works

The node reads `_pi_sprint` (bool) published by Player Input. If `True`, it uses `Sprint Speed` instead of `Speed`. The `Sprint Speed` socket allows overriding the value from the graph.

## How Accel Lerp works

An internal velocity (`_pm_vx`, `_pm_vy`) is maintained that lerps toward the target speed each frame:

```
current_speed = current_speed + (target - current_speed) * accel_lerp
```

With `Accel Lerp = 1.0` movement is identical to original behavior (instant). Lower values cause the character to accelerate and decelerate gradually.

## How Air Control works

Reads `_pj_in_air` from **Player Jump** (if present). While the player is airborne, the target velocity is multiplied by `Air Control` before the lerp, reducing aerial maneuverability.

| Air Control | Effect |
|-------------|--------|
| `1.0` | Full air control (no restriction) |
| `0.3` | 30% of normal control in the air (recommended) |
| `0.0` | No air control |

!!! warning "Requires Player Input on the same object"
    If Player Input is not present, `_pi_move_x/y` and `_pi_sprint` do not exist and the object will not move.

!!! info "Air Control requires Player Jump"
    If Player Jump is not in the graph, `_pj_in_air` defaults to `False`, equivalent to Air Control = 1.0 at all times.

## Example graph

```
[OnUpdate] → [Player Input] → [Player Jump] → [Player Movement] → [Out]
```
