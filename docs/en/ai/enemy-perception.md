# Enemy Perception

**Type:** Exec (In → Out)  
**Category:** AI  
**Execution Order:** 40 (first in the modular system)

Detects a target using distance, field-of-view (FOV), and a raycast. Publishes results as `ai_*` variables that the rest of the modular system reads.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Target | Enum | *(scene objects)* | Object to detect |
| View Distance | Float (≥0.1) | `12.0` | Maximum detection range in world units |
| Aggro Radius | Float (≥0.0) | `2.5` | Always detects within this radius, ignoring FOV and raycast |
| Use FOV | Bool | `True` | Limit detection to a forward cone |
| FOV Angle | Float (1–360) | `90.0` | Total cone width in degrees |
| Use Raycast | Bool | `True` | Require unobstructed line of sight via raycast |
| Raycast Prop | String | `""` | BGE property to filter raycast obstacles; empty = any object |
| Memory Duration | Float (≥0.0) | `3.0` | Seconds to remember target after losing sight (0 = instant forget) |
| Perception Interval | Float (≥0.0) | `0.0` | Seconds between full detection checks (0 = every frame) |
| Debug | Bool | `False` | Print perception state to console |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `self.ai_can_see` | bool | True if target is visible this frame |
| `self.ai_has_line_of_sight` | bool | True if raycast reaches target unobstructed |
| `self.ai_dist` | float | XY distance to target (9999 if missing) |
| `self.ai_target` | KX_GameObject\|None | Target object reference |
| `self.ai_target_pos` | Vector\|None | Target position if visible; None if hidden |
| `self.ai_last_seen_pos` | Vector\|None | Last known position (persists after losing sight) |

## Behavior

### Detection logic (each check)

```
If distance ≤ aggro_radius:
    → instant detection (no FOV, no raycast)
If distance ≤ view_distance:
    If use_fov:
        → compute dot product with enemy's Y forward axis
        → if dot ≥ cos(fov_angle/2): continue
    If use_raycast:
        → rayCast to target
        → detect only if hit == target
    If not use_raycast:
        → detect by distance + FOV alone
```

### Perception Interval

If `Perception Interval > 0`, the full detection check only runs every N seconds. This saves CPU in levels with many enemies. Published variables (`ai_can_see`, etc.) only update on those frames.

### Memory

When the target leaves view, `ai_last_seen_pos` retains the last known position for `memory_duration` seconds. After that, `ai_target` is set to None.

`ai_can_see = False` does **not** clear `ai_last_seen_pos` — this lets AI State Machine send the enemy to search at that point.

## Use in the modular system

Enemy Perception must run **before** AI State Machine. The `execution_order = 40` ensures its variables are current when the SM reads them:

```
[OnUpdate] → [Enemy Perception] → [Damage Receiver] → [AI State Machine] → ...
```

## Configuration examples

### Guard with forward cone and 5-second memory

```
Target: Player
View Distance: 15.0
Aggro Radius: 2.0
Use FOV: True
FOV Angle: 120.0
Use Raycast: True
Memory Duration: 5.0
Perception Interval: 0.1  (updates every 100ms)
```

### Omniscient enemy with no FOV

```
Use FOV: False
Use Raycast: False
View Distance: 20.0
```

## Notes

- Distance for FOV and memory is computed on the XY plane — height doesn't affect detection.
- The FOV cosine is computed at compile time, not runtime, to save CPU.
- If the target is destroyed during play, all `ai_*` variables reset to safe defaults without errors.
- Debug mode prints `see`, `dist`, and `los` to the console every frame — useful for tuning parameters.
