# Add To Property

**Type:** Action  
**Category:** LOGIC

Adds a value to a BGE property on the object (`self.own[prop] += amount`).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Property | String | `"score"` | BGE property name |
| Amount | Float | `1.0` | Value to add. Can be negative to subtract |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| Amount | Input | Data (Float) |

## Generated code

```python
self.own['score'] += 1.0
```

## Typical usage

### Accumulate points

```
[On Collision: Property="pickup"] → [Add To Property: prop="score", Amount=10]
```

### Count frames in a state

```
[On Update] → [On State: FSM ID="ai", State="patrol"]
                  └── True ──► [Add To Property: prop="patrol_frames", Amount=1]
```

### Reduce health (subtract)

```
[On Message: Subject="take_damage"] → [Add To Property: prop="health", Amount=-10]
```

## Notes

- The property must exist on the object as a numeric type. If it doesn't exist or is a string, Python will raise `TypeError`.
- To write an absolute value, use [Set Property](../data/set-property.md).
