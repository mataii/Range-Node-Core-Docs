# Vector Math

**Type:** Data  
**Category:** MATH

Performs a mathematical operation between two 3D vectors and returns one component of the result or a scalar.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Operation | Enum | `ADD` | See operation table |
| A | Vector | `[0,0,0]` | First vector |
| B | Vector | `[0,0,0]` | Second vector (ignored for LEN and NORM) |
| Component | Enum | `X` | Component to return: `X`, `Y`, `Z`, or `W` for scalars (DOT, LEN) |

## Available operations

| Operation | Result |
|-----------|--------|
| ADD | `A + B` |
| SUB | `A - B` |
| MUL | Component-wise multiplication |
| SCALE | `A * B[0]` (scale by first element of B) |
| DOT | Dot product (scalar) |
| CROSS | Cross product |
| LEN | Length of A (scalar) |
| NORM | Normalized A |
| LERP | Linear interpolation between A and B with factor B[3] |
| REFLECT | Reflection of A over normal B |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| A | Input | Data (Vector) |
| B | Input | Data (Vector) |
| Result | Output | Data (Float) |

## Typical usage

### Calculate velocity length

```
[On Update] → [BT Condition: {Vector Math: A={linearVelocity}, Op=LEN} > 5.0]
                  └── True ──► [...]  # object moving fast
```

### Dot product for attack angle check

```
[On Update] → [BTCustomTask:
                   dot = {Vector Math: A={my_forward}, B={to_player_dir}, Op=DOT}
                   # dot > 0.7 ≈ facing me (≈ 45°)]
```

## Notes

- To access the DOT or LEN scalar result, use `Component=W` (the node's convention for a scalar result).
- Input vectors can come from BGE properties, `worldPosition`, `linearVelocity`, etc.
