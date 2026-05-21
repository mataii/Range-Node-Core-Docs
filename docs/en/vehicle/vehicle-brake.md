# Vehicle Brake

**Type:** Action  
**Category:** VEHICLE

Applies braking force to the specified vehicle wheels.

## Properties

| Property | Default | Description |
|----------|---------|-------------|
| Force | `20.0` | Braking force (higher = harder braking) |
| Wheels | `All` | Affected wheels: `All`, `Front`, `Rear` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Force | Input | Data (Float) |
| Out | Output | Exec |

## Generated code

```python
if hasattr(self, '_vehicle'):
    for _bi in range(4):
        if _bi < self._vehicle.getNumWheels():
            self._vehicle.applyBraking(20.0, _bi)
```

## Typical usage

### Handbrake (Space)

```
[On Key Press: Key=SPACE, Mode=HELD]
    → [Vehicle Brake: Force=25, Wheels=All]

[On Key Release: Key=SPACE]
    → [Vehicle Brake: Force=0, Wheels=All]  # release brake
```

### Rear brake for drifting

```
[On Key Press: Key=SPACE, Mode=HELD]
    → [Vehicle Brake: Force=30, Wheels=Rear]
```

## Notes

- Applying `Force=0` releases the brake on those wheels.
- Combining `Vehicle Throttle` with `Force=0` and `Vehicle Brake` with high force gives effective stopping.
