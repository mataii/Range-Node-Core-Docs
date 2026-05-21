# Get Mouse Delta

**Type:** Data  
**Category:** CURSOR

Returns the X or Y delta computed by [Capture Mouse](capture-mouse.md) in the current frame. Must be used on the same object that runs `Capture Mouse`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Capture ID | String | `"cam"` | Must match the ID used in `Capture Mouse` |
| Axis | Enum | `X` | `X` = horizontal, `Y` = vertical |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Delta | Output | Data (Float) |

## Generated code

```python
# Axis = X
getattr(self, '_mc_cam_dx', 0.0)

# Axis = Y
getattr(self, '_mc_cam_dy', 0.0)
```

## Typical usage

### Apply mouselook rotation

```
[On Update] → [BTCustomTask:
                   dx = {Get Mouse Delta: Capture ID="cam", Axis=X}
                   dy = {Get Mouse Delta: Capture ID="cam", Axis=Y}
                   self.own.applyRotation([0, 0, -dx], False)]
```

## Notes

- If `Capture Mouse` hasn't run yet, returns `0.0` (attribute doesn't exist; `getattr` uses the fallback).
- The ID must exactly match the one in `Capture Mouse`, including case.
