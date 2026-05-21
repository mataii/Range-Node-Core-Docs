# Set Linear Velocity

**Type:** Action  
**Category:** PHYSICS

Directly sets the linear velocity of the object.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| X | Float | `0.0` | X velocity |
| Y | Float | `0.0` | Y velocity |
| Z | Float | `0.0` | Z velocity |
| Local | Bool | `False` | If True, applies in local space |
| Preserve Z | Bool | `False` | If True, keeps the current Z velocity |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| X, Y, Z | Input | Data (Float) |

## Generated code

```python
# Preserve Z = False
self.own.setLinearVelocity([0.0, 5.0, 0.0], False)

# Preserve Z = True
_vel = list(self.own.linearVelocity)
self.own.setLinearVelocity([0.0, 5.0, _vel[2]], False)
```

## Typical usage

### Move character with precise velocity

```
[On Update] → [Set Linear Velocity: Y=5.0, Local=True, Preserve Z=True]
```

### Abruptly stop an object

```
[On Message: Subject="stop"] → [Set Linear Velocity: X=0, Y=0, Z=0]
```

### Projectile with initial velocity

```
[On Start] → [Set Linear Velocity: Y=20.0, Local=True]
```

## Notes

- Unlike `applyForce`, `setLinearVelocity` overwrites the current velocity instead of accumulating it.
- `Preserve Z=True` is key for characters that need to move horizontally without affecting fall/jump.
