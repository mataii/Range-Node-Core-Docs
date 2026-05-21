# Set Property

**Type:** Action  
**Category:** DATA

Writes a value to a BGE property on the object running the component.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Property | String | `"prop"` | BGE property name |
| Value | Any | `0` | Value to write. Accepts data socket |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| Value | Input | Data (any type) |

## Generated code

```python
self.own['score'] = 0
```

## Typical usage

### Reset score

```
[On Start] → [Set Property: prop="score", Value=0]
```

### Update health after taking damage

```
[On Message: Subject="take_damage"]
    → [BTCustomTask: hp = self.own.get('health',100) - 10]
    → [Set Property: prop="health", Value={hp}]
```

### Mark object as visited

```
[On Collision: Object="player"] → [Set Property: prop="visited", Value=True]
```

## Notes

- If the property doesn't exist on the object yet, it is created automatically with the type of the assigned value.
- BGE may implicitly convert types — use the correct type to avoid unexpected behavior.
