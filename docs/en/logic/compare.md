# Compare

**Type:** Data  
**Category:** LOGIC

Compares two values and returns a boolean. Data version of `If Property` — has no Exec sockets.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Operator | Enum | `==` | `==`, `!=`, `<`, `<=`, `>`, `>=` |
| A | Any | `0` | First operand |
| B | Any | `0` | Second operand |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| A | Input | Data |
| B | Input | Data |
| Result | Output | Data (Bool) |

## Generated code

```python
(self.own.get('health', None) <= 0)
```

Pure expression inserted wherever the socket is connected.

## Typical usage

### Compound condition with Logic Gate

```
[On Update] → [Branch:
                  Condition = {Logic Gate:
                                   A = {Compare: A={health}, B=0, Op=>}
                                   B = {Compare: A={ammo}, B=0, Op=>}
                                   Op = AND}]
                  └── True ──► [Player Movement]
```

### Pass boolean to another node

```
[BT Set Blackboard: can_attack = {Compare: A={health}, B=20, Op=>=}]
```

## Notes

- Data-only node — no Exec sockets.
- For direct branching without an additional `Branch` node, use [If Property](if-property.md) or [BT Condition](../ai-bt/bt-condition.md).
