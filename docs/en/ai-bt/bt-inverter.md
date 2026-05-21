# BT Inverter

**Type:** Exec (In → Out)  
**Category:** AI_BT

Decorator that flips the result of the previous node. `SUCCESS` becomes `FAILURE` and vice versa. `RUNNING` passes through unchanged.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Transformation table

| `_bt_last_result` input | `_bt_last_result` output |
|-------------------------|--------------------------|
| `'SUCCESS'` | `'FAILURE'` |
| `'FAILURE'` | `'SUCCESS'` |
| `'RUNNING'` | `'RUNNING'` (unchanged) |

## Typical usage

Converts a positive condition into a negative one without changing the node that generates it:

```
# "Attack only if NOT on cooldown"
[BT Condition: in_cooldown] → [BT Inverter] → [BT Selector]
                                                    ├── Success ──► [Attack]
                                                    └── Failure ──► [Wait]
```

Without Inverter you would need to invert the logic in BTCondition. With Inverter, you reuse the same BTCondition for multiple purposes.

## Another use: convert failure to success for Sequence

```
# "Continue the sequence even if the door-open attempt fails"
[BT Custom Task: try_open_door] → [BT Inverter]
# If open_door fails (FAILURE) → Inverter gives SUCCESS → Sequence continues
```

## Graph position

Must go **immediately after** the node whose result you want to invert and **before** the composite that reads it:

```
✓  [BTCondition] → [BTInverter] → [BTSelector]
✗  [BTInverter] → [BTCondition] → [BTSelector]
```

## Notes

- Has no configurable properties.
- Does not affect the blackboard.
- Multiple inverters chained are valid (two inverters = no effect).
