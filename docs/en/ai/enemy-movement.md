# Enemy Movement

**Type:** Exec (In → Out)  
**Category:** AI  
**Execution Order:** 55 (after Navigation Follow Path)

Moves the owner object toward `ai_move_target` with smoothed velocity. Publishes `ai_is_moving` so Enemy Animation knows whether to play Walk or Idle.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Speed | Float (≥0.01) | `3.0` | Movement speed (units/s) |
| Stop Radius | Float (≥0.0) | `1.0` | Distance at which the enemy stops |
| Vel Lerp | Float (0.01–1.0) | `0.2` | Velocity smoothing (1=instant) |
| Physics | Enum | `Character` | Physics mode |

### Physics modes

| Mode | Method |
|------|--------|
| `Character` | `applyMovement()` × `deltaTime()` |
| `Dynamic` | Assigns `localLinearVelocity.x/y` (preserves Z) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Variables read

| Variable | Source | Description |
|----------|--------|-------------|
| `ai_move_target` | Navigation Follow Path / AI State Machine | Navigation destination |
| `ai_target_pos` | Enemy Perception | Fallback if no nav target |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `self.ai_is_moving` | bool | True if speed > 0.1 |

## Behavior

Destination priority:
1. `ai_move_target` (Navigation Follow Path redirects this to the current waypoint)
2. `ai_target_pos` (direct target position, if no navigation)

If the destination is a valid Vector:
- Computes XY direction to destination.
- If `distance > stop_radius`: interpolates velocity toward desired direction.
- If `distance ≤ stop_radius`: decelerates to zero.

If no valid destination: stops completely.

## Typical usage

### Basic movement toward target

```
[Enemy Perception] → [AI State Machine] → [Enemy Movement]
```

### With waypoint navigation

```
[AI State Machine] → [Navigation Pathfinder] → [Navigation Follow Path] → [Enemy Movement]
```

Navigation Follow Path redirects `ai_move_target` to the current waypoint; Enemy Movement follows it.

### Different speeds by state

Enemy Movement has one speed. For different speeds per state (slow patrol, fast chase), use two Enemy Movement nodes in parallel branches:

```
[Enemy Decision]
    ├── Chase ──► [Enemy Movement: Speed=5.0]
    └── Idle  ──► [Enemy Movement: Speed=2.0]
```

## Notes

- The Z axis is ignored in movement direction.
- `ai_is_moving` is consumed by Enemy Animation to choose between Walk and Idle.
- `_emv_vel` is the internal smoothed velocity state. If two Enemy Movement nodes are in the same component, they share this variable — rename nodes to create separate variables.
- If both `ai_move_target` and `ai_target_pos` are None, the node stops without error.
