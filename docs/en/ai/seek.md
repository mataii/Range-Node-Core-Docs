# Seek

**Type:** Exec (In → Out)  
**Category:** AI

Moves the owner object toward a scene target with progressive arrival braking. Unlike BTMoveTo, Seek uses a smoothed arrival zone: it starts decelerating before the stop radius to avoid abrupt stops.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Target | Enum | *(scene objects)* | Destination object |
| Speed | Float (≥0.01) | `3.0` | Maximum speed (units/s) |
| Stop Radius | Float (≥0.0) | `1.0` | Distance at which the object stops |
| Vel Lerp | Float (0.01–1.0) | `0.2` | Velocity smoothing (1=instant) |
| Physics | Enum | `Character` | Object physics mode |

### Physics modes

| Mode | Movement method |
|------|-----------------|
| `Character` | `applyMovement()` in world space |
| `Dynamic` | Assigns `localLinearVelocity.x/y` (preserves Z) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Behavior

### Arrival zone

Seek calculates an automatic braking zone:

```
arrive_zone = max(stop_radius * 2 + 1, stop_radius + 1)
```

While the distance to the target is greater than `stop_radius`, the node scales speed by an `arrive` factor from 0 to 1:

```
arrive = clamp((distance - stop_radius) / arrive_zone, 0, 1)
desired_velocity = direction.normalized() * speed * arrive
```

This produces smooth deceleration instead of stopping abruptly.

### Persistence

The node stores the current velocity in `self._sk_vel` (Vector3). If the object is destroyed and respawned, the velocity resets.

### Missing target

If the Target object doesn't exist in the scene, the node does `pass` — no movement, no error.

## Typical usage

### Follow the player

```
Target: Player
Speed: 4.0
Stop Radius: 1.5
Vel Lerp: 0.15
```

```
[OnUpdate] → [Seek: Player]
```

### Seek combined with Distance Check

```
[Distance Check: Player, Threshold=15.0]
    ├── Near ──► [Seek: Player]
    └── Far  ──► [Wander]
```

## Difference from BTMoveTo

| Feature | Seek | BTMoveTo |
|---------|------|---------|
| System | Native AI | BT Task |
| Arrival braking | Yes (smooth zone) | No (hard stop) |
| BT result | N/A | `SUCCESS`/`FAILURE`/`RUNNING` |
| Dynamic target | Yes (re-lookup each frame) | Edit-time only |

## Notes

- The Z axis is ignored in movement direction — displacement is always horizontal.
- `Vel Lerp = 1.0` produces instant movement with no inertia. Low values (0.05–0.15) produce smooth acceleration and deceleration.
- Multiple Seek nodes in the same tree share `self._sk_vel` — use only one Seek per object or rename nodes to differentiate them.
