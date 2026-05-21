# Math

**Type:** Data  
**Category:** MATH

Performs an arithmetic operation between two values A and B.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Operation | Enum | `ADD` | `ADD`, `SUB`, `MUL`, `DIV` |
| A | Float | `0.0` | First operand |
| B | Float | `1.0` | Second operand |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| A | Input | Data (Float) |
| B | Input | Data (Float) |
| Result | Output | Data (Float) |

## Generated code

```python
# ADD
(A + B)

# SUB
(A - B)

# MUL
(A * B)

# DIV
(A / B) if B != 0 else 0
```

Division includes a zero guard.

## Typical usage

### Calculate normalized progress

```
[On Update] → [Set Bar:
                  Value = {Math: A={current}, B={total}, Op=DIV}]
```

### Scale speed

```
[Player Movement: Speed = {Math: A={base_speed}, B={sprint_mult}, Op=MUL}]
```

### Calculate health as percentage

```
[On Update] → [Set Text:
                  Text = {Number To String:
                              Value = {Math: A={health}, B=100, Op=MUL}}]
```

## Notes

- Data-only node — no Exec sockets.
- Division by zero returns `0` instead of raising `ZeroDivisionError`.
