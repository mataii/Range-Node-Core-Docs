# Apply Torque

**Type:** Action  
**Category:** PHYSICS

Applies a torque to the object, causing rotation on the specified axes.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| X | Float | `0.0` | Torque on X axis |
| Y | Float | `0.0` | Torque on Y axis |
| Z | Float | `0.0` | Torque on Z axis |
| Local | Bool | `False` | If True, applies in local space |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own.applyTorque([0.0, 0.0, 10.0], False)
```

## Typical usage

### Spin object on impact

```
[On Collision] → [Apply Torque: Z=50.0, Local=True]
```

### Vehicle instability effect

```
[On Update] → [Apply Torque: X={random * 5.0}, Local=True]
```

## Notes

- `applyTorque` only works on objects with dynamic physics (not static, not CHARACTER).
- Torque accumulates with angular damping — use [Set Damping](set-damping.md) to control how long it spins.
