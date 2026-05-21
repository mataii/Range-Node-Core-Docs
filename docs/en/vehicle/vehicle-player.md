# Vehicle Player

**Type:** Action  
**Category:** VEHICLE

All-in-one controller for player-driven vehicles. Includes wheel initialization, engine, brakes, steering, optional follow camera, turbo, and automatic rollover reset.

## Main properties

### Wheels and suspension

| Property | Default | Description |
|----------|---------|-------------|
| Front Left / Right / Rear Left / Right | — | The four wheel objects |
| Wheel Radius | `0.35` | Wheel radius |
| Stiffness / Damping / Compression | `50/10/4` | Suspension parameters |
| Tyre Friction | `4.0` | Tyre friction coefficient |
| Roll Influence | `0.05` | Roll influence |

### Engine

| Property | Default | Description |
|----------|---------|-------------|
| Drive Type | `RWD` | Drive: AWD / RWD / FWD |
| Max Engine Force | `800.0` | Maximum engine force |
| Max Brake Force | `25.0` | Maximum braking force |
| Max Steer (rad) | `0.5` | Maximum steering angle |
| Max Speed (km/h) | `120.0` | Top speed |
| Steer Return | `0.08` | Speed at which steering returns to center |

### Key bindings

W/S (throttle/reverse), A/D (steering), Space (brake), Shift (optional turbo), Ctrl (creep reverse)

### Extras

| Property | Default | Description |
|----------|---------|-------------|
| Turbo | `False` | Force multiplier with turbo key |
| Auto-Reset on Flip | `True` | Upright the vehicle on rollover |
| Store Speed in Property | `False` | Stores speed in `self.own['speed_kmh']` |

### Camera (optional)

| Property | Default | Description |
|----------|---------|-------------|
| Camera | — | Camera object in the scene |
| Cam Distance | `6.0` | Camera distance from chassis |
| Cam Height | `2.5` | Camera height above chassis |
| Cam Smoothing | `0.1` | Camera position smoothing (lerp) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Typical usage

```
[On Update] → [Vehicle Player:
                   Front Left=wheel_fl  Front Right=wheel_fr
                   Rear Left=wheel_rl   Rear Right=wheel_rr
                   Drive=RWD
                   Max Engine=800  Max Speed=120
                   Camera=follow_cam
                   Auto-Reset=True]
```

## Notes

- Connect to `On Update` — manages all vehicle logic every frame.
- The object must have **Vehicle** physics type in Range Game Engine.
- For finer control, use [Vehicle Setup](vehicle-setup.md) + [Vehicle Throttle](vehicle-throttle.md) + [Vehicle Steering](vehicle-steering.md) separately.
