# BT Selector

**Type:** Branch (In → Success / Failure / Out)  
**Category:** AI_BT  
**Logic:** OR

Reads `_bt_last_result` from the previous node and branches execution: if the result was `SUCCESS` it goes through the **Success** branch; any other value (`FAILURE` or `RUNNING`) goes through **Failure**.

Equivalent to the classic BT Selector node: *"try the first child; if it fails, try the next"*. In RNC, the "next child" is literally the next branch connected.

## Sockets

| Socket | Direction | Type | Description |
|--------|-----------|------|-------------|
| In | Input | Exec | |
| Success | Output | Exec | Fires if `_bt_last_result == 'SUCCESS'` |
| Failure | Output | Exec | Fires if `_bt_last_result != 'SUCCESS'` |
| Out | Output | Exec | Always fires (after Success or Failure) |

## Truth table

| `_bt_last_result` | Branch activated |
|-------------------|------------------|
| `'SUCCESS'` | Success |
| `'FAILURE'` | Failure |
| `'RUNNING'` | Failure |
| *(undefined)* | Failure (default `'FAILURE'`) |

## Internal variable

| Variable | Type | Description |
|----------|------|-------------|
| `_bt_selector_result` | string | Copy of the read result (for debugging) |

## Typical usage

```
[BT Move To: target] ──► [BT Selector]
                                ├── Success ──► [BT Play Animation: "attack"]
                                └── Failure ──► [BT Wait: 2.0s]
```

In this example, if the agent reaches the target (`SUCCESS`), it attacks. If it can't move (`FAILURE`) or is still on the way (`RUNNING`), it waits.

## Multi-option OR pattern

To implement "try A, if not B, if not C", chain Selectors:

```
[BT Condition: enemy_visible]
    ├── True ──► [BT Move To: enemy] → [BT Selector]
    │                                       ├── Success ──► [Attack]
    │                                       └── Failure ──►
    │                                                       [BT Move To: cover] → [BT Selector]
    │                                                           ├── Success ──► [Wait in cover]
    │                                                           └── Failure ──► [BT Wait: idle]
    └── False ──► [Patrol]
```

## Notes

- The `Out` socket always fires regardless of result. Use it for logic that must happen in all cases (clean state, debug).
- The node does not modify `_bt_last_result` — it preserves it so the next node can also read it if needed.
