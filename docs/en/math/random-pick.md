# Random Pick

**Type:** Data  
**Category:** MATH

Selects and returns a random element from a list of values using `random.choice`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Items | String | `"[]"` | Python list as a string, evaluated with `eval()` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (any type) |

## Generated code

```python
__import__('random').choice(['idle', 'patrol', 'wander'])
```

## Typical usage

### Select random AI state

```
[On State Enter: FSM ID="ai", State="choosing"]
    └── On Enter ──► [Set State:
                          State = {Random Pick: Items=['patrol','wander','guard']}]
```

### Select random sound effect

```
[On Collision: Property="footstep"]
    → [Play Sound: File={Random Pick: Items=['step1.wav','step2.wav','step3.wav']}]
```

### Assign random color

```
[On Start] → [BTCustomTask:
                  colors = [[1,0,0,1],[0,1,0,1],[0,0,1,1]]
                  self.own.color = {Random Pick: Items=colors}]
```

## Notes

- The list is evaluated with `eval()` at compile time. Can contain any valid Python type: strings, numbers, nested lists.
- If the list is empty, `random.choice` raises `IndexError`.
