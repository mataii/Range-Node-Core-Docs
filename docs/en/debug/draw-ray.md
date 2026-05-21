# Draw Ray

**Type:** Action  
**Category:** DEBUG

Draws a ray along a local axis of the object for the current frame. Computes direction from the object's world orientation and calls `Range.render.drawLine()`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Axis | Enum | `X` | Local axis: X, Y, Z, -X, -Y, -Z |
| Length | Float | `1.0` | Ray length in world units |
| Color | Vector (RGB) | `[1,0,0]` | Line color |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
_dr_ori = self.own.worldOrientation
_dr_dir = _dr_ori.col[0]  # local X axis
_dr_start = list(self.own.worldPosition)
_dr_end   = [_dr_start[i] + _dr_dir[i] * 1.0 for i in range(3)]
Range.render.drawLine(_dr_start, _dr_end, [1,0,0])
```

Column index changes by axis: X=0, Y=1, Z=2. Negative axes multiply by -1.

## Typical usage

### Visualize object's forward direction

```
[On Update] → [Draw Ray: Axis=Y, Length=2.0, Color=[0,1,0]]
```

### Debug weapon orientation

```
[On Update] → [Draw Ray: Axis=Z, Length=5.0, Color=[1,0,0]]
```

### Check AI attack axis

```
[On Update] → [Draw Ray: Axis=-Y, Length=3.0, Color=[1,0.5,0]]
```

## Difference from Draw Line

| Draw Ray | Draw Line |
|----------|-----------|
| Always starts at `self.own.worldPosition` | Configurable start/end points |
| Direction relative to object's local axis | Absolute world coordinates |
| Rotates with the object automatically | Fixed points or linked to other objects |

## Notes

- Only visible on the frame it executes. Use from `On Update` for a continuous visualization.
- `worldOrientation.col[i]` returns the column vector of the rotation matrix, which corresponds to the object's local axis in world space.
