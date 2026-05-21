# Set Angular Velocity

**Type:** Action  
**Category:** PHYSICS

Directly sets the angular velocity of the object in radians per second.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| X | Float | `0.0` | Angular velocity on X axis (rad/s) |
| Y | Float | `0.0` | Angular velocity on Y axis (rad/s) |
| Z | Float | `0.0` | Angular velocity on Z axis (rad/s) |
| Local | Bool | `False` | If True, in local space |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own.setAngularVelocity([0.0, 0.0, 3.14], False)
```

## Typical usage

### Stop object rotation

```
[On Message: Subject="freeze"] → [Set Angular Velocity: X=0, Y=0, Z=0]
```

### Spin object at constant speed

```
[On Start] → [Set Angular Velocity: Z=1.0, Local=True]
```

## Notes

- `setAngularVelocity` overwrites the current rotational velocity.
- Angular damping (configured with [Set Damping](set-damping.md)) will reduce the speed over time.
