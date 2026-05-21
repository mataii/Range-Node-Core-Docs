# Set Gravity

**Type:** Action  
**Category:** PHYSICS

Sets the global gravity vector for the scene in Range Game Engine.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| X | Float | `0.0` | X component of gravity |
| Y | Float | `0.0` | Y component of gravity |
| Z | Float | `-9.8` | Z component of gravity |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
Range.logic.setGravity([0.0, 0.0, -9.8])
```

## Typical usage

### Standard gravity

```
[On Start] → [Set Gravity: X=0, Y=0, Z=-9.8]
```

### Lunar gravity

```
[On Start] → [Set Gravity: X=0, Y=0, Z=-1.6]
```

### Zero gravity (space)

```
[On Start] → [Set Gravity: X=0, Y=0, Z=0.0]
```

## Notes

- Affects all physics objects in the scene simultaneously.
- The change is immediate and persists until called again or the scene changes.
- Can be used with [On Key Press](../input/on-key-press.md) for gravity-inversion mechanics.
