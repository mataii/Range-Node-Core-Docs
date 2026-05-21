# Vehicle Autonomous

**Type:** Action  
**Category:** VEHICLE

AI-driven vehicle. Can follow a target or patrol between waypoints, with optional obstacle avoidance and automatic rollover reset.

## Main properties

### Wheels and suspension

Same parameters as [Vehicle Setup](vehicle-setup.md): Front Left/Right, Rear Left/Right, Wheel Radius, Stiffness, Damping, Compression, Tyre Friction, Roll Influence.

### AI behaviour

| Property | Default | Description |
|----------|---------|-------------|
| Mode | `Follow Target` | `Follow Target` (pursues an object) or `Patrol` (visits waypoints in order) |
| Target | — | Object to follow (`Follow Target` only) |
| Waypoints | `2` | Number of waypoints (2–6, `Patrol` only) |
| WP 1–6 | — | Waypoint objects in patrol order |

### AI engine

| Property | Default | Description |
|----------|---------|-------------|
| Drive Type | `RWD` | Drive type |
| Max Engine Force | `600.0` | Maximum engine force |
| Max Brake Force | `20.0` | Maximum braking force |
| Max Steer (rad) | `0.45` | Maximum steering angle |
| Max Speed (km/h) | `60.0` | Top speed |
| Steer Sensitivity | `2.5` | Steering correction factor |
| Stop Distance | `3.0` | Distance at which braking starts |
| Arrive Radius | `4.0` | Radius within which a waypoint is considered reached |
| Brake Anticipation | `0.6` | Reduce engine force in turns to prevent skidding |

### Extras

| Property | Default | Description |
|----------|---------|-------------|
| Obstacle Avoidance | `True` | Side raycasts to steer around obstacles |
| Auto-Reset on Flip | `True` | Upright the vehicle on rollover |
| Store Speed in Property | `False` | Stores speed in `self.own['speed_kmh']` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| Arrived | Output | Exec |

## Typical usage

### Vehicle that chases the player

```
[On Update] → [Vehicle Autonomous:
                   Mode=Follow Target, Target=player
                   Max Speed=60  Stop Distance=4.0
                   Obstacle Avoidance=True]
                  └── Out     ──► [...]
                  └── Arrived ──► [...]  # when it reaches the target
```

### Taxi on patrol route

```
[On Update] → [Vehicle Autonomous:
                   Mode=Patrol
                   WP 1=stop_a  WP 2=stop_b  WP 3=stop_c
                   Max Speed=40  Arrive Radius=3.0]
```

## Notes

- The object must have **Vehicle** physics type in Range Game Engine.
- The `Arrived` branch fires every frame while the vehicle is within `Stop Distance` of the target.
- `Obstacle Avoidance` uses lateral raycasts of 7 units — adjust `Stop Distance` to avoid false positives.
