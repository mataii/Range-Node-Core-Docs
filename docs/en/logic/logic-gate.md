# Logic Gate

**Type:** Data  
**Category:** LOGIC

Performs a logical operation between two boolean values and returns the result.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Operation | Enum | `AND` | `AND`, `OR`, `NOT`, `NAND`, `NOR`, `XOR`, `XNOR` |
| A | Bool | `False` | First operand |
| B | Bool | `False` | Second operand (ignored for `NOT`) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| A | Input | Data (Bool) |
| B | Input | Data (Bool) |
| Result | Output | Data (Bool) |

## Operation table

| Operation | Result |
|-----------|--------|
| AND | `A and B` |
| OR | `A or B` |
| NOT | `not A` |
| NAND | `not (A and B)` |
| NOR | `not (A or B)` |
| XOR | `A != B` |
| XNOR | `A == B` |

## Typical usage

### Move only if alive and grounded

```
[On Update] → [Branch:
                  Condition = {Logic Gate:
                                   A = {Compare: health > 0}
                                   B = {Compare: on_ground == True}
                                   Op = AND}]
                  └── True ──► [Player Movement]
```

### Attack only if not reloading or animating

```
[On Update] → [Branch:
                  Condition = {Logic Gate:
                                   A = {is_reloading}
                                   B = {is_animating}
                                   Op = NOR}]
                  └── True ──► [Weapon Fire Executor]
```

## Notes

- Data-only node — no Exec sockets. Combine its output with [Branch](branch.md) for branching.
- For `NOT`, only socket `A` is evaluated. Socket `B` is ignored.
