# Gamepad Axis

**Type:** Data  
**Category:** GAMEPAD

Returns the value of a gamepad analog axis with deadzone applied. The result is in the range [-1.0, 1.0].

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Gamepad Index | Int | `0` | Device slot (0–7) |
| Axis Index | Int | `0` | Axis index according to the OS driver |
| Deadzone | Float | `0.1` | Minimum absolute value to register as real movement |
| Invert | Bool | `False` | Inverts the sign of the value |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (Float) |

## Generated code

```python
(_gp0_ax := Range.logic.joysticks[0].axisValues[0] if Range.logic.joysticks[0] else 0.0,
 _gp0_ax if abs(_gp0_ax) > 0.1 else 0.0)[-1]
```

With `invert=True`, the result is multiplied by `-1`.

## Typical usage

### Move character with left stick

```
[On Update] → [BTCustomTask:
                   move_x = {Gamepad Axis: Index=0, Axis=0}
                   move_y = {Gamepad Axis: Index=0, Axis=1}
                   self.own.applyMovement([move_x, move_y, 0], True)]
```

### Rotate camera with right stick

```
[On Update] → [BTCustomTask:
                   rot_x = {Gamepad Axis: Index=0, Axis=2}
                   rot_y = {Gamepad Axis: Index=0, Axis=3, Invert=True}
                   # apply rotation to camera...]
```

## Notes

- Uses the walrus operator (`:=`) to avoid evaluating `joysticks[idx]` twice.
- Returns `0.0` when the gamepad is not connected.
- Axis indices vary by controller model and driver. Axis 0 is typically left stick X, 1 is left stick Y, 2 is right stick X, 3 is right stick Y.
