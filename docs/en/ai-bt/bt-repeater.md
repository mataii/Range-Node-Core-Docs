# BT Repeater

**Type:** Exec (In → Out)  
**Category:** AI_BT

Decorator that accumulates `SUCCESS` results from the previous node. Returns `RUNNING` until N successes are accumulated; upon reaching them it returns `SUCCESS` and resets the counter.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Repeat N | Int (≥1) | `3` | Number of `SUCCESS` results to accumulate before propagating `SUCCESS` |
| Reset on Failure | Bool | `True` | Resets the counter if the child node returns `FAILURE` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Per-frame logic

```
frame
  │
  ├── _bt_last_result == 'SUCCESS':
  │       counter += 1
  │       If counter >= N:
  │           counter = 0
  │           _bt_last_result = 'SUCCESS'   ← propagates success
  │       Else:
  │           _bt_last_result = 'RUNNING'   ← still accumulating
  │
  ├── _bt_last_result == 'FAILURE' (if Reset on Failure = True):
  │       counter = 0
  │       _bt_last_result = 'FAILURE'
  │
  └── _bt_last_result == 'RUNNING':
          no change
```

## Internal state

The counter uses the node name to generate a unique variable: `self._btrep_<sid>`. This allows multiple BTRepeaters on the same object with independent counters.

## Typical usage

### Confirm detection before reacting

```
[BT Condition: enemy_in_sight] → [BT Repeater: N=5] → [BT Selector]
                                                              ├── Success ──► [Alert and attack]
                                                              └── Failure ──► (no reaction)
```

The agent only alerts if it sees the enemy for 5 consecutive frames (avoids reactions to fleeting glimpses).

### Attack N times before retreating

```
[BT Custom Task: attack] → [BT Repeater: N=3, Reset on Failure=False] → [BT Sequence]
                                                                               ├── Continue ──► [Retreat]
                                                                               └── Abort ──► (keeps attacking)
```

## Notes

- The counter persists between frames — it is cumulative state, not per-frame.
- If `Reset on Failure = False`, failures don't reset the counter (FAILURE propagates but the count stays where it was).
- `RUNNING` from the child doesn't modify the counter — the Repeater waits for the child to resolve `SUCCESS` or `FAILURE`.
