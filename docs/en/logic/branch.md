# Branch

**Type:** Branch (True / False)  
**Category:** LOGIC

Generic if/else branch. Evaluates the condition connected to the `Condition` socket and routes execution to the corresponding path.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Condition | Input | Data (Bool) |
| True | Output | Exec (condition true) |
| False | Output | Exec (condition false) |

## Generated code

```python
if condition_value:
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

## Typical usage

### Condition on Compare result

```
[On Update] → [Branch: Condition={Compare: A={score}, B=100, Op=>=}]
                  └── True ──► [Node Broadcast: Subject="level_complete"]
```

### Condition on Logic Gate

```
[On Update] → [Branch:
                  Condition = {Logic Gate: A={is_alive}, B={is_grounded}, Op=AND}]
                  └── True ──► [Player Movement]
```

### Condition on boolean property

```
[On Update] → [Branch: Condition={Get Property: prop="is_visible"}]
                  ├── True ──► [...]
                  └── False ──► [...]
```

## Notes

- `Branch` is the generic if/else node. For more specific comparisons, use [If Property](if-property.md) (which includes the operator) or [BT Condition](../ai-bt/bt-condition.md) (arbitrary Python expression).
- The condition can come from any data node that returns a value evaluable as boolean.
