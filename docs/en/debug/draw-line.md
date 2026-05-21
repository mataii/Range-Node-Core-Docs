# Draw Line

**Type:** Action  
**Category:** DEBUG

Draws a 3D line between two world-space points for the current frame. Uses `Range.render.drawLine()`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| From | Vector | `[0,0,0]` | Start point in world coordinates. If `from_self=True`, uses the object's position |
| To | Vector | `[0,0,1]` | End point in world coordinates. If `to_obj` is set, uses its position |
| Color | Vector (RGB) | `[1,0,0]` | Line color |
| From Self | Bool | `False` | If True, the start point is `self.own.worldPosition` |
| To Object | String | `""` | Scene object name. If not empty, the end point is its `worldPosition` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
Range.render.drawLine([0,0,0], [0,0,1], [1,0,0])
```

With `from_self=True` and `to_obj="target"`:

```python
_dl_from = list(self.own.worldPosition)
_dl_to   = list(scene.objects['target'].worldPosition)
Range.render.drawLine(_dl_from, _dl_to, [1,0,0])
```

## Typical usage

### Visualize movement direction

```
[On Update] → [Draw Line:
                   From Self = True
                   To = {Get Property: prop="move_dir"}
                   Color = [0,1,0]]
```

### Trace a line between two objects

```
[On Update] → [Draw Line:
                   From Self = True
                   To Object = "enemy"
                   Color = [1,0,0]]
```

### Visualize a target position

```
[On Update] → [Draw Line:
                   From = [0,0,0]
                   To Object = "waypoint"
                   Color = [0,0,1]]
```

## Notes

- The line exists only for the frame it executes on. To see it continuously, call the node every frame from `On Update`.
- `Range.render.drawLine` accepts lists or tuples of 3 floats for position and color.
- Color has no alpha channel — the line is always opaque.
- Remove before shipping.
