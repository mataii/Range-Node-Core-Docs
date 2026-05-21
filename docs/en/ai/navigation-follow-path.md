# Navigation Follow Path

**Type:** Exec (In → Out)  
**Category:** AI  
**Execution Order:** 54 (after Navigation Pathfinder, before Enemy Movement)

Follows the path computed by Navigation Pathfinder, advancing waypoint by waypoint. Redirects `ai_move_target` to the current waypoint so Enemy Movement heads in the right direction. Detects if the enemy is stuck.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Waypoint Tolerance | Float (≥0.1) | `1.0` | XY distance to consider a waypoint reached |
| Stuck Timeout | Float (≥0.1) | `2.0` | Seconds without moving before resetting the path |
| Stuck Speed | Float (≥0.01) | `0.1` | Minimum speed (units/s) to not be considered stuck |
| Debug | Bool | `False` | Print waypoint and stuck events to console |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Variables read

| Variable | Source |
|----------|--------|
| `ai_nav_path` | Navigation Pathfinder |
| `ai_nav_index` | Navigation Pathfinder |
| `ai_state` | AI State Machine |

## Published / modified variables

| Variable | Type | Description |
|----------|------|-------------|
| `self.ai_reached_destination` | bool | True when path is complete |
| `self.ai_move_target` | Vector | Redirected to current path waypoint |
| `self.ai_nav_index` | int | Incremented on waypoint arrival |

## Behavior

### Per frame

1. Reads `ai_nav_path` and `ai_nav_index`.
2. If path is empty or state is IDLE/PATROL/DEAD: resets and does nothing.
3. If index is past the end of the path: `ai_reached_destination = True`.
4. Computes XY distance to the current waypoint.
5. If `distance ≤ waypoint_tolerance`: advances `ai_nav_index`, redirects `ai_move_target` to the next waypoint.
6. While still en-route: keeps `ai_move_target` pointing at the current waypoint.

### Stuck detection

Every frame compares the current position with the previous frame's position:
- If estimated speed < `stuck_speed` for ≥ `stuck_timeout` seconds: resets `ai_nav_index` to 0.
- This forces Navigation Pathfinder to recompute the path on the next cycle.

Stuck detection runs independently of navigation state.

## Data flow

```
NavigationPathfinder → ai_nav_path = [wp0, wp1, wp2, destination]
                        ai_nav_index = 0

NavigationFollowPath → ai_move_target = path[0]         (frame 1)
                     → ai_move_target = path[1]         (when wp0 reached)
                     → ai_move_target = destination     (when wp2 reached)
                     → ai_reached_destination = True

EnemyMovement → reads ai_move_target and moves toward it
```

## Typical usage

```
[Navigation Pathfinder] → [Navigation Follow Path] → [Enemy Movement]
```

### Without scene waypoints

If Navigation Pathfinder found no waypoints and the path is `[destination]`:
- Navigation Follow Path follows it like a single waypoint.
- Equivalent to direct movement with no pathfinding.

## Notes

- Waypoint tolerance is compared on the **XY plane** — height difference doesn't prevent waypoint advancement.
- The stuck reset sets the index back to 0 but doesn't clear the path — the path stays until Navigation Pathfinder decides to recompute.
- `ai_reached_destination` is True only when the last waypoint (the final destination) is reached.
- If the path changes between frames (because Pathfinder recomputed), the index is also reset to 0 by the Pathfinder automatically.
