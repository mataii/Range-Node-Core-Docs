# Random Int

**Type:** Data  
**Category:** MATH

Returns a random integer in the range [min, max] using `random.randint`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Min | Int | `0` | Minimum value (inclusive) |
| Max | Int | `10` | Maximum value (inclusive) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (Int) |

## Generated code

```python
__import__('random').randint(0, 10)
```

## Typical usage

### Select random dialogue line

```
[On Start] → [Set Property: prop="line_idx", Value={Random Int: Min=0, Max=4}]
```

### Random damage

```
[Weapon Fire Executor] → [BTCustomTask:
                              dmg = {Random Int: Min=8, Max=15}
                              target.own['health'] -= dmg]
```

### Spawn object at random grid position

```
[On Start] → [BTCustomTask:
                  col = {Random Int: Min=0, Max=7}
                  row = {Random Int: Min=0, Max=7}
                  self.own.worldPosition = [col * 2.0, row * 2.0, 0]]
```

## Notes

- `randint(a, b)` includes both endpoints (`a <= N <= b`).
- For floats, use [Random Float](random-float.md).
