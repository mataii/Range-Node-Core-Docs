# On Mouse Move

**Type:** Branch (Moving / Still)  
**Category:** EVENT

Detects cursor movement between the current frame and the previous one. Branches execution and exposes the movement delta.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Threshold | Float | `0.001` | Minimum delta (in normalized units) to consider the mouse as moving |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Moving | Output | Exec (cursor moved beyond threshold) |
| Still | Output | Exec (cursor within threshold) |
| Delta X | Output | Data (Float — horizontal movement this frame) |
| Delta Y | Output | Data (Float — vertical movement this frame) |

## Coordinate system

Mouse position is normalized in the range `[0.0, 1.0]`:
- `(0.0, 0.0)` — bottom-left corner
- `(1.0, 1.0)` — top-right corner

**Delta X / Delta Y** are the differences between the current and previous frame position. Positive Delta X means the cursor moved right; positive Delta Y means the cursor moved up.

## Generated code

```python
_mm_cur = Range.logic.mouse.position
_mm_dx = _mm_cur[0] - self._mm_last[0]
_mm_dy = _mm_cur[1] - self._mm_last[1]
self._mm_last = _mm_cur

if abs(_mm_dx) > 0.001 or abs(_mm_dy) > 0.001:
    # Moving path
else:
    # Still path
```

`self._mm_last` is initialized to `(0.5, 0.5)` on the first frame.

## Typical usage

### Camera rotation

```
[On Update] → [On Mouse Move]
                  └── Moving ──► [BTCustomTask:
                                     cam.applyRotation([0, 0, -_mm_dx * sensitivity], False)
                                     cam.applyRotation([-_mm_dy * sensitivity, 0, 0], True)]
```

### UI hover detection

```
[On Update] → [On Mouse Move]
                  ├── Moving ──► [BTCustomTask: update_cursor_highlight(_mm_dx, _mm_dy)]
                  └── Still  ──► (nothing)
```

### Mouse look with sensitivity

```
[On Update] → [On Mouse Move: Threshold=0.0005]
                  └── Moving ──► [BTCustomTask:
                                     yaw   = _mm_dx * 120 * Range.logic.deltaTime()
                                     pitch = _mm_dy * 80  * Range.logic.deltaTime()
                                     self.own.applyRotation([0, 0, -yaw], False)]
```

### Detect mouse idle

```
[On Update] → [On Mouse Move]
                  └── Still ──► [Delay: 3.0s]
                                    └── True ──► [BTCustomTask: activate_screensaver()]
```

## Notes

- `_mm_dx` and `_mm_dy` are local variables available in any node connected to the Moving or Still paths.
- The threshold filters out sub-pixel jitter from sensors — lower it only if you need very fine precision.
- Delta values depend on the screen resolution and OS mouse speed — apply a sensitivity multiplier for consistent behavior across systems.
- On Mouse Move reads `Range.logic.mouse.position`, which is the OS cursor position. If the cursor is locked (first-person mode), use `Range.logic.mouse.deltaPosition` instead, via a [BTCustomTask](../ai-bt/bt-custom-task.md).
- Multiple On Mouse Move nodes in the same tree each maintain their own `_mm_last` variable (namespaced by node name).
