# Capture Mouse

**Type:** Action  
**Category:** CURSOR

Captures mouse movement every frame and stores the delta in object attributes. Designed to implement mouselook without manual code.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Capture ID | String | `"cam"` | Unique identifier for this capture |
| Sensitivity | Float | `0.3` | Delta multiplier |
| Invert X | Bool | `False` | Inverts the horizontal axis |
| Invert Y | Bool | `False` | Inverts the vertical axis |
| Hide Cursor | Bool | `True` | Automatically hides the cursor when active |
| Clamp | Float | `90.0` | Pitch limit in degrees (0 = no limit) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code (simplified)

```python
_mc_pos = Range.logic.mouse.position
_mc_cx, _mc_cy = 0.5, 0.5
self._mc_cam_dx = (_mc_pos[0] - _mc_cx) * 0.3
self._mc_cam_dy = (_mc_pos[1] - _mc_cy) * 0.3
Range.logic.mouse.position = [_mc_cx, _mc_cy]
if True:  # hide_cursor
    Range.logic.mouse.visible = False
```

Deltas are stored in `self._mc_<capture_id>_dx` and `self._mc_<capture_id>_dy`.

## Typical usage

### Basic mouselook (first person)

```
[On Update] → [Capture Mouse: Capture ID="player", Sensitivity=0.3]
[On Update] → [BTCustomTask:
                   dx = {Get Mouse Delta: Capture ID="player", Axis=X}
                   dy = {Get Mouse Delta: Capture ID="player", Axis=Y}
                   self.own.applyRotation([0, 0, -dx], False)
                   cam = scene.objects['Camera']
                   cam.applyRotation([dy, 0, 0], True)]
```

## Notes

- `Capture Mouse` must run on the **same object** as `Get Mouse Delta` so they read the same attribute.
- The cursor is automatically centered every frame (writes `mouse.position = [0.5, 0.5]`).
- If `Hide Cursor = False`, the cursor remains visible and can leave the window.
- For multiple capture sources on the same object, use different `Capture ID` values.
