# BT Move To

**Type:** Exec (In → Out)  
**Category:** AI_BT  
**Task**

Moves the owner object toward a scene target. Returns `RUNNING` while moving, `SUCCESS` upon arriving within the arrival distance, and `FAILURE` if the target does not exist in the scene.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Target | Enum | *(scene objects)* | Name of the destination object |
| Speed | Float (≥0.01) | `3.0` | Movement speed (units/s) |
| Arrive Dist | Float (≥0.05) | `1.0` | Distance at which arrival is considered |
| Vel Lerp | Float (0.01–1.0) | `0.2` | Velocity smoothing (1 = instant, lower = smoother) |
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

## Results

| Condition | `_bt_last_result` |
|-----------|-------------------|
| Target does not exist in scene | `'FAILURE'` |
| Distance to target ≤ Arrive Dist | `'SUCCESS'` |
| In transit | `'RUNNING'` |

## Internal movement

The node maintains an internal velocity (`_btmv_<sid>_vel`) that lerps toward the desired direction each frame:

```
direction = (target_pos - own_pos).normalized() * Speed
current_vel = lerp(current_vel, direction, Vel Lerp)
```

The Z axis is ignored in direction calculation — movement is always horizontal.

On arrival or `FAILURE`, velocity is brought to zero.

## Typical usage

### Follow the player

```
Target: Player
Speed: 4.0
Arrive Dist: 1.5
```

```
[BT Condition: enemy_visible]
    ├── True ──► [BT Move To: Player] → [BT Selector]
    │                                        ├── Success ──► [BT Custom Task: attack]
    │                                        └── Failure ──► [BT Wait: 0.3s]
    └── False ──► [BT Move To: patrol_point]
```

### Patrol between waypoints

Combined with BTSetBlackboard to dynamically change the destination:

```
[BT Move To: Waypoint_A] → [BT Selector]
                                ├── Success ──► [BT Set Blackboard: mode="go_B"]
                                └── Failure ──► [BT Wait: 0.5s]
```

## Notes

- `Target` is selected at **edit time** from the active scene objects. If the object doesn't exist at runtime, returns `FAILURE`.
- For dynamic targets (player in another scene, runtime-instantiated object), use **BTCustomTask** with direct `applyMovement` and position from the blackboard.
- `Vel Lerp = 1.0` gives instant movement (no inertia). `Vel Lerp = 0.05` gives very gradual acceleration.
- Velocity state (`_btmv_<sid>_vel`) uses the node name — if you have two BTMoveTo nodes in the same tree, each has its own internal velocity.
