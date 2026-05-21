# Transform

**Type:** Action  
**Category:** OBJECT

Moves, rotates, or scales the object. Uses `applyMovement`, `applyRotation`, or `localScale` depending on the mode.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Mode | Enum | `MOVE` | `MOVE`, `ROTATE`, `SCALE` |
| X | Float | `0.0` | X component |
| Y | Float | `0.0` | Y component |
| Z | Float | `0.0` | Z component |
| Local | Bool | `True` | If True, applies in the object's local space |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| X | Input | Data (Float) |
| Y | Input | Data (Float) |
| Z | Input | Data (Float) |

## Generated code

```python
# MOVE
self.own.applyMovement([0.0, 0.0, 0.0], True)

# ROTATE (in radians)
self.own.applyRotation([0.0, 0.0, 0.0], True)

# SCALE (absolute scale, not relative)
self.own.localScale = [1.0, 1.0, 1.0]
```

## Typical usage

### Move object forward

```
[On Update] → [Transform: Mode=MOVE, Y=0.1, Local=True]
```

### Rotate around Z axis each frame

```
[On Update] → [Transform: Mode=ROTATE, Z=0.05, Local=True]
```

### Scale object to specific size

```
[On Start] → [Transform: Mode=SCALE, X=2.0, Y=2.0, Z=2.0]
```

## Notes

- For `ROTATE`, values are in radians. 0.05 rad/frame ≈ 3°/frame at 60fps = ~180°/s.
- For `SCALE`, values are absolute scale — not multiplicative. To double size: `[2, 2, 2]`.
- `Local=False` uses world coordinates for MOVE and ROTATE.
