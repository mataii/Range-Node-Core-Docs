# Navigation Pathfinder

**Type:** Exec (In → Out)  
**Category:** AI  
**Execution Order:** 52 (after AI State Machine, before Navigation Follow Path)

Computes a path using BFS over a scene waypoint graph. The result is stored in `ai_nav_path` for Navigation Follow Path to follow.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Waypoint Prop | String | `"waypoint"` | BGE property name that identifies waypoint objects |
| Connect Radius | Float (≥0.5) | `6.0` | Maximum distance to connect two waypoints as graph neighbors |
| Repath Interval | Float (≥0.05) | `0.5` | Seconds between path recomputes |
| Move Threshold | Float (≥0.1) | `1.0` | Destination must shift this far to force an early repath |
| Debug | Bool | `False` | Print path info to console |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `self.ai_nav_path` | list[Vector] | List of path positions |
| `self.ai_nav_index` | int | Current waypoint index (initialized to 0) |
| `self.ai_nav_repath_timer` | float | Seconds since last recompute |

## Variables read

| Variable | Source |
|----------|--------|
| `ai_move_target` | AI State Machine |
| `ai_target_pos` | Enemy Perception (fallback) |
| `ai_state` | AI State Machine |

## Behavior

### Activation

The pathfinder only operates when:
- `ai_state` is `CHASE` or `SEARCH`
- A valid `ai_move_target` (Vector) exists

In other states (IDLE, PATROL, DEAD), the path is cleared.

### Graph construction

1. Collects all scene objects with the BGE property `waypoint_prop`.
2. Connects pairs of waypoints with distance ≤ `connect_radius`.
3. Result: undirected proximity graph.

> Waypoints must have the BGE property added in the Range property editor. For example: property `waypoint = True` (Boolean type).

### BFS

1. Finds the waypoint nearest to the owner's position → start node.
2. Finds the waypoint nearest to the destination → end node.
3. BFS from start to end → list of waypoint names.
4. Converts names to Vector positions → `ai_nav_path`.
5. Appends the exact destination position at the end of the path.

### Fallback without waypoints

If no waypoints exist in the scene, the path is simply `[destination]` — straight-line movement.

### Repath conditions

The path is recomputed when:
- The cooldown timer (`_nav_repath_cd`) reaches 0 (every `repath_interval` seconds)
- The destination has shifted more than `move_threshold` units from the last computation

## Waypoint setup in the scene

1. Create Empty objects or any objects in the scene.
2. Add the BGE property (e.g., `waypoint`) to each one.
3. Place them in the patrol area with spacing ≤ `connect_radius` between neighbors.
4. The pathfinder auto-detects them at runtime.

```
WP_A ──── WP_B ──── WP_C
  └─────────────────┘
  (connected if distance ≤ connect_radius)
```

## Typical usage

```
[AI State Machine] → [Navigation Pathfinder] → [Navigation Follow Path] → [Enemy Movement]
```

## Notes

- The graph is rebuilt on each repath, not cached between frames (waypoints can move).
- If two waypoints are within `connect_radius` but separated by a wall, the pathfinder still connects them — it doesn't raycast between waypoints. For maps with complex walls, tune `connect_radius` or place waypoints strategically.
- The path always ends with the exact destination position, so the enemy reaches the precise point even if the nearest waypoint isn't very close.
- `ai_nav_index` resets to 0 on each new recompute. Navigation Follow Path increments it as the enemy advances.
