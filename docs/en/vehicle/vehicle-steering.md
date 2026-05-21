# Vehicle Steering

**Type:** Action  
**Category:** VEHICLE

Sets the steering angle of the vehicle's front wheels.

## Properties

| Property | Default | Description |
|----------|---------|-------------|
| Angle (rad) | `0.0` | Steering angle in radians (negative = left, positive = right) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Angle | Input | Data (Float, in radians) |
| Out | Output | Exec |

## Generated code

```python
if hasattr(self, '_vehicle'):
    for _si in [0, 1]:
        if _si < self._vehicle.getNumWheels():
            self._vehicle.setSteeringValue(0.5, _si)
```

## Typical usage

### Steering with A/D keys

```
[On Key Press: Key=A, Mode=HELD] → [Vehicle Steering: Angle=0.5]
[On Key Press: Key=D, Mode=HELD] → [Vehicle Steering: Angle=-0.5]
[On Key Release: Key=A]          → [Vehicle Steering: Angle=0.0]
[On Key Release: Key=D]          → [Vehicle Steering: Angle=0.0]
```

### Steering with gamepad axis

```
[On Update]
    → [Vehicle Steering:
           Angle={Math: {Gamepad Axis: Axis=Left X} * -0.5}]
```

## Notes

- Typical range is `-0.5` to `0.5` radians (~29°). Larger values may cause erratic behaviour.
- Only affects the front wheels (indices 0 and 1).
- For progressive steering return to center, use `Vehicle Player` which includes `Steer Return`.
