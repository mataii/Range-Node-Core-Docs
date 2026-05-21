# Get Property

**Type:** Data  
**Category:** DATA

Reads a BGE property from the object running the component. Returns the value or `None` if it doesn't exist.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Property | String | `"prop"` | BGE property name |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (any type) |

## Generated code

```python
self.own.get('score', None)
```

## Typical usage

### Read score to display on HUD

```
[On Update] → [Set Text: Text = {Get Property: prop="score"}]
```

### Condition on property

```
[On Update] → [BT Condition: {Get Property: prop="health"} <= 0]
                  └── True ──► [Node Broadcast: Subject="player_dead"]
```

### Pass property to another node

```
[Set State: State = {Get Property: prop="target_state"}]
```

## Notes

- Uses `self.own.get(prop, None)` — if the property doesn't exist, returns `None` without error.
- To write, use [Set Property](set-property.md).
- BGE properties can be `int`, `float`, `bool`, or `str`.
