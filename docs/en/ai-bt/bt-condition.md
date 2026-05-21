# BT Condition

**Type:** Branch (In → True / False)  
**Category:** AI_BT

Reads a blackboard key, evaluates a Python expression, and branches execution. Also sets `_bt_last_result` based on the result, so a subsequent Selector or Sequence can read it.

It is the **decision-making** node of the tree: the "question" that determines which branch activates.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| BB Key | String | `""` | Blackboard key to read. Its value is available as `val` in the condition |
| Condition | String | `"val == True"` | Python expression. `val` = blackboard value, `self` = component |

## Sockets

| Socket | Direction | Type | Description |
|--------|-----------|------|-------------|
| In | Input | Exec | |
| True | Output | Exec | Fires if the condition is true |
| False | Output | Exec | Fires if the condition is false |

## Affected variables

| Variable | Value after execution |
|----------|-----------------------|
| `_bt_last_result` | `'SUCCESS'` if condition is `True`, `'FAILURE'` if `False` |

## Example expressions

### Simple comparison
```
BB Key:    enemy_visible
Condition: val == True
```

### Distance threshold
```
BB Key:    dist_to_player
Condition: val is not None and val < 10.0
```

### Check state type
```
BB Key:    ai_state
Condition: val in ('PATROL', 'IDLE')
```

### Without blackboard (direct condition on self)
```
BB Key:    (empty)
Condition: self.own.worldPosition.z > 0.5
```

When `BB Key` is empty, `val` will be `None`. You can ignore `val` and write any Python expression using `self`.

### Check object BGE property
```
BB Key:    (empty)
Condition: self.own.get('hp', 100) > 20
```

## Error handling

If the expression throws an exception (wrong variable name, method not found, etc.), the result is `False` (→ False branch, `_bt_last_result = 'FAILURE'`). No crash occurs.

## Graph position

```
[OnUpdate] → [BT Simple Parallel: update enemy_visible]
                    │
                    Out ──► [BT Condition: enemy_visible]
                                  ├── True ──► [BT Move To: enemy]
                                  └── False ──► [BT Move To: patrol_point]
```

## Notes

- BTCondition is **not just a decorator** — it is also a flow node (like Branch in FLOW). Its True/False outputs can connect to completely different tree branches.
- Unlike BTInverter, BTCondition **defines** the result rather than transforming it.
- To evaluate multiple conditions with AND/OR, use a compound expression: `val and self.own.get('hp', 0) > 0`.
