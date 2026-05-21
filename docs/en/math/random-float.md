# Random Float

**Type:** Data  
**Category:** MATH

Returns a random float in the range [min, max] using `random.uniform`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Min | Float | `0.0` | Minimum value (inclusive) |
| Max | Float | `1.0` | Maximum value (inclusive) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (Float) |

## Generated code

```python
__import__('random').uniform(0.0, 1.0)
```

## Typical usage

### Random sound pitch

```
[On Collision: Property="pickup"] → [Play Sound:
                                         Pitch = {Random Float: Min=0.9, Max=1.1}]
```

### Random spawn position

```
[On Start] → [BTCustomTask:
                  x = {Random Float: Min=-5.0, Max=5.0}
                  y = {Random Float: Min=-5.0, Max=5.0}
                  self.own.worldPosition = [x, y, 0]]
```

### Random cooldown between attacks

```
[On State Enter: FSM ID="ai", State="attack"]
    └── On Enter ──► [BTCustomTask:
                          self._bt_bb['attack_cd'] = {Random Float: Min=1.0, Max=3.0}]
```

## Notes

- Each call generates a different value. For reproducibility, set a seed with [Set Random Seed](set-random-seed.md).
- `random.uniform(a, b)` includes both endpoints.
