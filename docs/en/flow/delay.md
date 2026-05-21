# Delay

**Type:** Branch (True / False)  
**Category:** FLOW

Executes the `True` path after N seconds have elapsed since the node was first activated. While waiting, executes the `False` path. With `Repeat=True` it re-fires every N seconds (heartbeat mode).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Delay (s) | Float (≥0.0) | `1.0` | Seconds to wait before firing |
| Repeat | Bool | `False` | If True, re-fires every N seconds indefinitely |

The `Delay` data socket can override the property at runtime.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Delay | Input | Data (Float) |
| True | Output | Exec (when time expires) |
| False | Output | Exec (while waiting) |

## Behavior

### First execution frame

Initializes the timer with the target fire time:

```python
self._dly_<name> = Range.logic.getClockTime() + delay
self._dly_<name>_done = False
```

### Subsequent frames

```python
if not self._dly_<name>_done and Range.logic.getClockTime() >= self._dly_<name>:
    if repeat:
        self._dly_<name> = Range.logic.getClockTime() + delay  # re-arm
    else:
        self._dly_<name>_done = True  # fire once only
    # → True path
else:
    # → False path
```

### Implementation with `getClockTime()`

Delay uses Range's **absolute clock** (`getClockTime()`), not deltaTime accumulation. This means:

- Precision is framerate-independent.
- The target time is fixed at the moment of initialization (first frame the node executes).
- **Must be connected to OnUpdate** to check the clock every frame.

### Repeat = False (default)

Fires `True` exactly once and then enters silent mode (`_done = True`). After that, **neither True nor False executes** — the execution chain ends at that node.

### Repeat = True

Every time it fires, it re-arms the timer with `currentTime + delay`. This produces a periodic heartbeat for as long as the node keeps receiving execution.

## Typical usage

### Wait 2 seconds before an action

```
[OnUpdate] → [Delay: 2.0s, Repeat=False]
                 ├── True  ──► [BTCustomTask: trigger_cutscene()]
                 └── False ──► (waiting)
```

### Heartbeat every 0.5 seconds (periodic polling)

```
[OnUpdate] → [Delay: 0.5s, Repeat=True]
                 ├── True  ──► [Enemy Perception]   # perception every half second
                 └── False ──► (nothing between ticks)
```

### Dynamic delay from socket

```python
# In a BT Set Blackboard or directly in the Data socket:
Value: self._bt_bb.get('respawn_time', 3.0)
```

```
[Delay: Delay = self._bt_bb['respawn_time']]
```

### Combined with Do Once for a single delayed action

```
[OnUpdate] → [Do Once]
                 └── True ──► [Delay: 5.0s, Repeat=False]
                                  └── True ──► [BTCustomTask: start_ambush()]
```

### Blinking light with Flip Flop

```
[OnUpdate] → [Delay: 0.3s, Repeat=True]
                 └── True ──► [Flip Flop]
                                 ├── True  ──► [BTCustomTask: lamp.energy = 5.0]
                                 └── False ──► [BTCustomTask: lamp.energy = 0.0]
```

## Comparison with BT Wait

| Delay | BT Wait |
|-------|---------|
| FLOW category | AI_BT category |
| Connected directly to OnUpdate | Connected inside BT tree |
| False path = "while waiting" | No waiting path |
| Can repeat with Repeat=True | Restarts only on completion |
| Uses `getClockTime()` | Accumulates `deltaTime()` |

## Notes

- If `Delay = 0.0`: the node may fire on the same frame it initializes (because `getClockTime() >= getClockTime() + 0` is true). To avoid this, use a minimum value of `0.016`.
- With `Repeat=False`: once `_done = True`, the node **stops executing both paths** — neither True nor False. Execution simply ends at that node.
- With `Repeat=True`: the timer re-arms from the moment of firing, not from the start. Small framerate variations can cause interval jitter — this is an approximation, not a precision timer.
- For multiple independent delays, use Delay nodes with different names.
