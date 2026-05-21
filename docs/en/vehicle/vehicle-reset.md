# Vehicle Reset

**Type:** Action  
**Category:** VEHICLE

Detects whether the vehicle has flipped (local Z below a threshold) and automatically uprights it by lifting it and resetting its orientation and velocities.

## Properties

| Property | Default | Description |
|----------|---------|-------------|
| Tilt Threshold | `0.3` | Local Z axis threshold to detect rollover (0 = 90°, 1 = upright) |
| Lift Height | `1.5` | How far to lift the vehicle when resetting (in units) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| Flipped | Output | Exec |

## Generated code

```python
if self.own.localOrientation[2][2] < 0.3:
    self.own.worldPosition += mathutils.Vector((0, 0, 1.5))
    self.own.worldOrientation = mathutils.Matrix.Identity(3)
    self.own.linearVelocity   = mathutils.Vector((0, 0, 0))
    self.own.angularVelocity  = mathutils.Vector((0, 0, 0))
```

## Typical usage

### Automatic reset on rollover

```
[On Update]
    → [Vehicle Reset: Tilt Threshold=0.3, Lift Height=1.5]
          └── Out     ──► [...]  # normal continuation
          └── Flipped ──► [Set Text: Object=hud_msg, Text="Flipped!"]
```

### Manual reset with R key

```
[On Key Press: Key=R, Mode=PRESSED]
    → [Vehicle Reset: Tilt Threshold=0.0]  # always resets
```

## Notes

- The `0.3` threshold detects rollovers when the vehicle has tilted more than ~72° from vertical.
- The `Flipped` branch fires only when the reset is applied, not every frame the vehicle is flipped.
- `Vehicle Player` includes this functionality as an option (`Auto-Reset on Flip`).
