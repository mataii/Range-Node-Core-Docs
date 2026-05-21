# Vehicle Throttle

**Type:** Action  
**Category:** VEHICLE

Applies engine force to the vehicle's driven wheels. Requires [Vehicle Setup](vehicle-setup.md) to have run first.

## Properties

| Property | Default | Description |
|----------|---------|-------------|
| Force | `500.0` | Engine force (positive = forward, negative = reverse) |
| Drive | `RWD` | Drive type: `AWD` (all wheels), `RWD` (rear), `FWD` (front) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Force | Input | Data (Float) |
| Out | Output | Exec |

## Generated code

```python
# Drive = RWD
if hasattr(self, '_vehicle'):
    for _ti in [2, 3]:
        if _ti < self._vehicle.getNumWheels():
            self._vehicle.applyEngineForce(500.0, _ti)
```

## Typical usage

### Acceleration with W

```
[On Key Press: Key=W, Mode=HELD]
    → [Vehicle Throttle: Force=800, Drive=RWD]

[On Key Press: Key=S, Mode=HELD]
    → [Vehicle Throttle: Force=-400, Drive=RWD]
```

### Variable force (gamepad axis)

```
[On Update]
    → [Vehicle Throttle:
           Force={Math: {Gamepad Axis: Axis=Left Y} * 800.0}
           Drive=AWD]
```

## Notes

- Negative values apply force in reverse.
- Use [Vehicle Brake](vehicle-brake.md) to stop rather than applying negative force.
- Applying `0.0` force removes acceleration without braking.
