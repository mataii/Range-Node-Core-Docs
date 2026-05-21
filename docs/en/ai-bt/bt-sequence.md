# BT Sequence

**Type:** Branch (In → Continue / Abort / Out)  
**Category:** AI_BT  
**Logic:** AND

Reads `_bt_last_result` from the previous node and branches: if the result was **not** `FAILURE` it goes through **Continue**; if it was `FAILURE` it goes through **Abort** and sets `_bt_last_result = 'FAILURE'` to propagate it.

Equivalent to the classic BT Sequence node: *"execute all children in order; if any one fails, abort the entire sequence"*. In RNC, each "child" is a node connected in series before the Sequence.

## Sockets

| Socket | Direction | Type | Description |
|--------|-----------|------|-------------|
| In | Input | Exec | |
| Continue | Output | Exec | Fires if `_bt_last_result != 'FAILURE'` |
| Abort | Output | Exec | Fires if `_bt_last_result == 'FAILURE'` |
| Out | Output | Exec | Always fires |

## Truth table

| `_bt_last_result` | Branch activated | `_bt_last_result` after |
|-------------------|------------------|------------------------|
| `'SUCCESS'` | Continue | unchanged |
| `'RUNNING'` | Continue | unchanged |
| `'FAILURE'` | Abort | `'FAILURE'` (confirmed) |
| *(undefined)* | Continue (default `'SUCCESS'`) | unchanged |

## Typical usage

```
[BT Condition: has_ammo] ──► [BT Sequence]
                                   ├── Continue ──► [BT Move To: enemy] → [BT Sequence]
                                   │                                            ├── Continue ──► [BT Custom Task: shoot]
                                   │                                            └── Abort ──► [BT Wait: reposition]
                                   └── Abort ──► [BT Move To: ammo_pickup]
```

In this example: if the agent has ammo (`SUCCESS`), it advances toward the enemy. If it arrives (`SUCCESS`), it shoots. Any `FAILURE` in the chain aborts and goes to the alternative branch.

## Difference from Selector

| | Selector | Sequence |
|--|----------|----------|
| Continues if | `SUCCESS` | `SUCCESS` **or** `RUNNING` |
| Aborts if | `FAILURE` or `RUNNING` | `FAILURE` |
| Philosophy | "try alternatives" | "all steps must succeed" |

## Multi-step AND pattern

For a sequence of steps that must all complete:

```
[Step 1: detect target] → [BT Sequence]
                               ├── Continue ──► [Step 2: approach] → [BT Sequence]
                               │                                           ├── Continue ──► [Step 3: attack]
                               │                                           └── Abort ──► (path blocked)
                               └── Abort ──► (no target)
```

## Notes

- `RUNNING` **does not abort** the sequence — the current step continues until it resolves `SUCCESS` or `FAILURE`.
- When aborting, the node explicitly sets `_bt_last_result = 'FAILURE'`, allowing a parent Selector to detect it.
