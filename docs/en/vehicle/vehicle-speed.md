# Vehicle Speed

**Type:** Data  
**Category:** VEHICLE

Returns the current chassis speed in meters/second and km/h, calculated from `linearVelocity.length`.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| m/s | Output | Data (Float) |
| km/h | Output | Data (Float) |

## Generated code

```python
_spd_ms  = self.own.linearVelocity.length
_spd_kmh = _spd_ms * 3.6
```

## Typical usage

### Speedometer in HUD

```
[On Update]
    → [Set Text:
           Object=speedometer
           Text={Number To String:
                     Value={Vehicle Speed: Socket=km/h}
                     Decimals=0
                     Suffix=" km/h"}]
```

### Limit maximum speed

```
[On Update]
    → [BT Condition: {Vehicle Speed: Socket=km/h} < 120]
          └── True  ──► [Vehicle Throttle: Force=800]
          └── False ──► [Vehicle Throttle: Force=0]
```

## Notes

- Speed is the magnitude of the linear velocity vector — always positive.
- For direction (forward/reverse), check the dot product with the object's local Y vector.
- Does not require `Vehicle Setup` to work — reads `linearVelocity` directly.
