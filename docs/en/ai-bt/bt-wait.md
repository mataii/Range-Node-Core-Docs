# BT Wait

**Type:** Exec (In → Out)  
**Category:** AI_BT  
**Task**

Waits a fixed (or random) time before reporting `SUCCESS`. Returns `RUNNING` every frame while the timer has not expired.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Duration (s) | Float (≥0) | `1.0` | Seconds to wait |
| Randomize | Bool | `False` | Randomizes duration between `Duration` and `Duration + Variance` |
| Variance (s) | Float (≥0) | `0.5` | Random variance range (only if Randomize = True) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Results

| Condition | `_bt_last_result` |
|-----------|-------------------|
| Timer < Duration | `'RUNNING'` |
| Timer ≥ Duration | `'SUCCESS'` (and resets the timer) |

## Timer behavior

The timer initializes to `0.0` on first execution (using `hasattr` as a guard). Each frame it adds `deltaTime()`. When it reaches the target duration:

1. Resets to `0.0`.
2. If `Randomize = True`, calculates a new target duration for the next cycle.
3. Returns `SUCCESS`.

This allows using BTWait in **loops**: connected inside a cyclic Selector, it will wait N seconds, return `SUCCESS`, and the next frame the cycle resets it automatically.

## Typical usage

### Pause before resuming patrol

```
[BT Condition: reached_waypoint] 
    ├── True ──► [BT Wait: 2.0s] → [BT Selector]
    │                                    ├── Success ──► [BT Set Blackboard: next_waypoint]
    │                                    └── Failure ──► (keeps waiting)
    └── False ──► [BT Move To: waypoint]
```

### Random delay between actions (organic behavior)

```
[BT Custom Task: pick_action] → [BT Wait: Duration=0.5, Randomize=True, Variance=1.5]
```

The agent waits between 0.5 and 2.0 seconds before evaluating the next action.

### Action timeout (combined with BTRepeater)

```
[BT Move To: target] → [BT Repeater: N=60] → [BT Selector]
                  ^           (60 SUCCESS = ~60 frames en route)
```

Or directly with Wait to force a cooldown after failure:

```
[BT Sequence]
    ├── Continue ──► [BT Custom Task: attack] → [BT Sequence]
    │                   (if attack fails)            └── Abort ──► [BT Wait: 1.0s]
    └── Abort ──► ...
```

## Notes

- The timer state (`_btw_<sid>`) uses the node name in the editor to generate a unique identifier. Renaming the node resets the state.
- Duration `0.0` returns `SUCCESS` immediately (the timer starts at 0, the first comparison already meets the condition).
