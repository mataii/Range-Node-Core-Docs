# Patrol

**Type:** Exec (In → Out / Done)  
**Category:** AI

Moves the owner object sequentially between 2–8 scene waypoints. Supports Loop (cyclic) and non-loop (stops at last waypoint) modes. Includes arrival braking when approaching each waypoint.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Waypoints | Int (2–8) | `2` | Number of active waypoints |
| WP 1 … WP N | Enum | *(scene objects)* | Waypoints in visit order |
| Speed | Float (≥0.01) | `2.0` | Patrol speed (units/s) |
| Arrive Radius | Float (≥0.1) | `1.0` | Distance to consider a waypoint reached |
| Loop | Bool | `True` | Return to WP1 after the last waypoint |
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
| Out | Output | Exec (continuous) |
| Done | Output | Exec (when Loop=False and last WP reached) |

## Behavior

### Arrival braking

Like Seek, Patrol uses an automatic braking zone before each waypoint:

```
arrive_zone = max(arrive_radius * 3, arrive_radius + 1.5)
arrive = clamp((distance - arrive_radius) / arrive_zone, 0, 1)
```

### Persistent state

The node maintains `self._pt_idx` (current waypoint index) and `self._pt_wps` (name list). They are initialized only once.

### Loop = True

On reaching the last waypoint, the index wraps back to 0:
```python
self._pt_idx = (self._pt_idx + 1) % len(self._pt_wps)
```

### Loop = False

On reaching the last waypoint, the index no longer advances. The `Done` socket fires.

## Typical usage

### Three-point loop patrol

```
Waypoints: 3
WP 1: Waypoint_A
WP 2: Waypoint_B
WP 3: Waypoint_C
Speed: 2.0
Loop: True
```

```
[OnUpdate] → [Patrol] → [Out: idle / other logic]
                 │
                Done: (never fires if Loop=True)
```

### Patrol then Chase

```
[Distance Check: Player, Threshold=10.0]
    ├── Near ──► [Seek: Player]
    └── Far  ──► [Patrol]
```

## Notes

- Empty waypoint slots are filtered out — if WP 3 is empty and Waypoints=4, only the 3 named ones are used.
- If all waypoints are invalid, the node does `pass` without error.
- Patrol operates on the XY plane — Z is ignored in direction calculation.
- `_pt_idx` persists while the object exists. To restart from the beginning, use a BTCustomTask: `self._pt_idx = 0`.
- Character physics velocity is multiplied by `deltaTime()` internally; Dynamic physics sets velocity directly.
