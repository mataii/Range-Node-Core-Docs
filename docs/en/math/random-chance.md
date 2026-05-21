# Random Chance

**Type:** Branch (Success / Fail)  
**Category:** MATH

Branches by probability. Branch version of [Random Bool](random-bool.md) — combines random generation and branching in a single node.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Probability | Float | `0.5` | Success probability [0.0 – 1.0] |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Success | Output | Exec (success — occurs with P probability) |
| Fail | Output | Exec (fail — occurs with 1-P probability) |

## Generated code

```python
if __import__('random').random() < 0.5:
    #SUCCESS_PATH#
else:
    #FAIL_PATH#
```

## Typical usage

### AI decides to attack or retreat

```
[On State Enter: FSM ID="ai", State="decide"]
    └── On Enter ──► [Random Chance: Prob=0.7]
                         ├── Success ──► [Set State: State="attack"]
                         └── Fail ──► [Set State: State="retreat"]
```

### Item drop on death

```
[On Message: Subject="enemy_dead"] → [Random Chance: Prob=0.3]
                                         └── Success ──► [Add Object: Object="item_drop"]
```

## Notes

- Equivalent to `Branch` + `Random Bool`, but in one node. Use this when you only need to branch by probability without reusing the boolean value.
