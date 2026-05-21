# Timer

**Type:** Data  
**Category:** DATA

Returns the absolute system time in seconds since the Range Game Engine session started. Equivalent to `Range.logic.getClockTime()`.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Time | Output | Data (Float) |

## Generated code

```python
Range.logic.getClockTime()
```

## Typical usage

### Calculate elapsed time between events

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [BTCustomTask:
           self._bt_bb['last_jump_time'] = {Timer}]

# Later:
[On Update] → [BT Condition: {Timer} - self._bt_bb.get('last_jump_time', 0) > 1.5]
                  └── True ──► [...]  # cooldown expired
```

### Time-based animation using absolute clock

```
[On Update] → [Set Light Energy:
                   Energy = {Math: sin({Timer} * 2.0) * 0.5 + 1.0}]
```

## Notes

- `getClockTime()` is real-world seconds — not affected by game logic pause, but affected by OS time scaling.
- To measure relative intervals (time in an FSM state), use [Get State Time](../fsm/get-state-time.md).
- The value is a high-precision float — two calls in the same frame return nearly identical but not necessarily equal values.
