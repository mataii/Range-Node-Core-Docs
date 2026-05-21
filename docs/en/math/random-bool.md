# Random Bool

**Type:** Data  
**Category:** MATH

Returns `True` with the specified probability, `False` otherwise.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Probability | Float | `0.5` | Probability of True [0.0 – 1.0] |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (Bool) |

## Generated code

```python
(__import__('random').random() < 0.5)
```

## Typical usage

### Trigger event with 20% probability

```
[On Update] → [Branch: Condition={Random Bool: Prob=0.2}]
                  └── True ──► [Play Sound: File="sfx/ambient.wav"]
```

### Random AI behavior on idle enter

```
[On State Enter: FSM ID="ai", State="idle"]
    └── On Enter ──► [Branch: Condition={Random Bool: Prob=0.5}]
                         ├── True ──► [Set State: State="patrol"]
                         └── False ──► [Set State: State="wander"]
```

## Notes

- `Probability=0.0` always returns `False`, `Probability=1.0` always returns `True`.
- For direct branching by probability, use [Random Chance](random-chance.md).
