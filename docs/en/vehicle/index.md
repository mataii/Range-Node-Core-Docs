# VEHICLE

Nodes for creating and controlling vehicles with wheel physics (Vehicle Constraint) in Range Game Engine. Includes full controllers for player and AI.

## Nodes

| Node | Type | Description |
|------|------|-------------|
| [Vehicle Setup](vehicle-setup.md) | Action | Initialize the vehicle constraint and configure wheels |
| [Vehicle Throttle](vehicle-throttle.md) | Action | Apply engine force (AWD / RWD / FWD) |
| [Vehicle Brake](vehicle-brake.md) | Action | Apply braking force to wheels |
| [Vehicle Steering](vehicle-steering.md) | Action | Set the steering angle on the front wheels |
| [Vehicle Speed](vehicle-speed.md) | Data | Return current speed in m/s and km/h |
| [Vehicle Reset](vehicle-reset.md) | Action | Upright the vehicle if it has flipped |
| [Vehicle Player](vehicle-player.md) | Action | Full player vehicle controller with optional follow camera |
| [Vehicle Autonomous](vehicle-autonomous.md) | Action | AI-driven vehicle: follow a target or patrol waypoints |

## Typical flow (manual)

```
[On Update] → [Vehicle Setup: FL=wheel_fl, FR=wheel_fr, RL=wheel_rl, RR=wheel_rr]

[On Key Press: Key=W, Mode=HELD]
    → [Vehicle Throttle: Force=800, Drive=RWD]

[On Key Press: Key=A, Mode=HELD]
    → [Vehicle Steering: Angle=0.5]

[On Key Press: Key=SPACE, Mode=HELD]
    → [Vehicle Brake: Force=25, Wheels=All]
```

## Notes

- `Vehicle Setup` initializes only once (uses a `hasattr` guard) — safe to place in `On Update`.
- For rapid prototyping, use `Vehicle Player` which packs all logic into a single node.
- Wheels must be child objects of the chassis in the scene.
