# If Property

**Type:** Branch (True / False)  
**Category:** LOGIC

Compares a BGE property on the object against a value using one of 6 comparison operators.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Property | String | `"health"` | BGE property name |
| Operator | Enum | `==` | `==`, `!=`, `<`, `<=`, `>`, `>=` |
| Value | Any | `0` | Reference value to compare against |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Value | Input | Data |
| True | Output | Exec (condition true) |
| False | Output | Exec (condition false) |

## Generated code

```python
if self.own.get('health', None) <= 0:
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

## Typical usage

### Detect player death

```
[On Update] → [If Property: prop="health", Op=<=, Value=0]
                  └── True ──► [Set State: State="dead"]
```

### Verify inventory is not full

```
[On Collision: Property="pickup"] → [If Property: prop="item_count", Op=<, Value=10]
                                        └── True ──► [BTCustomTask: add_item()]
```

### Check if player is in zone

```
[On Update] → [If Property: prop="in_safe_zone", Op===, Value=True]
                  └── True ──► [...]
```

## Notes

- Uses `self.own.get(prop, None)` — if the property doesn't exist, the value is `None`, which can cause `TypeError` with `<`, `<=`, `>`, `>=` operators.
- For string comparisons, use `==` or `!=`. Other operators may produce unexpected results.
