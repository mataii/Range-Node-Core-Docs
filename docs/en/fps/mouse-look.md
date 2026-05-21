# Mouse Look

**Type:** Action  
**Category:** FPS

Controls the camera with the mouse: rotates the character body in Yaw (horizontal) and the camera in Pitch (vertical). Includes smoothing and pitch clamping.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Body | Object | — | Object that rotates in Yaw (horizontal). Empty = uses the component's object |
| Camera | Object | — | Object that rotates in Pitch (vertical). Usually the camera |
| Sensitivity | Float | `2.0` | Rotation speed with the mouse |
| Mouse Smooth | Float | `0.7` | Smoothing: 1 = instant, lower = smoother |
| Clamp Pitch (rad) | Float | `1.4` | Vertical rotation limit in radians (~80°) |
| Invert Y | Bool | `False` | Invert the mouse vertical axis |
| Yaw (Z) | Bool | `True` | Enable horizontal rotation |
| Pitch (X) | Bool | `True` | Enable vertical rotation |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
if not hasattr(self, '_ml_ready'):
    self._ml_pitch = 0.0
    self._ml_sx    = 0.0
    self._ml_sy    = 0.0
    Range.logic.mouse.visible  = False
    Range.logic.mouse.position = (0.5, 0.5)
    self._ml_ready = True
_ml_body = self.own.scene.objects.get('player_body') or self.own
_ml_cam  = self.own.scene.objects.get('camera')
_ml_ms   = Range.logic.mouse
_ml_rx   = (_ml_ms.position[0] - 0.5) * 2.0
_ml_ry   = (_ml_ms.position[1] - 0.5) * 2.0
_ml_ms.position = (0.5, 0.5)
self._ml_sx = self._ml_sx * 0.3 + _ml_rx * 0.7
self._ml_sy = self._ml_sy * 0.3 + _ml_ry * 0.7
_ml_body.applyRotation((0.0, 0.0, -self._ml_sx), False)
if _ml_cam:
    _ml_delta = -self._ml_sy
    if abs(self._ml_pitch + _ml_delta) <= 1.4:
        self._ml_pitch += _ml_delta
        _ml_cam.applyRotation((_ml_delta, 0.0, 0.0), True)
```

## Typical usage

### Manual FPS controller

```
[On Start] → [Capture Mouse]

[On Update] → [Mouse Look: Body=player_body, Camera=fps_camera, Sensitivity=2.5]
           → [...]  # WASD movement, jump, etc.
```

## Notes

- The mouse is hidden and centered automatically on the first frame.
- Stores `self._fps_cam` for use by [FPS Raycast](fps-raycast.md) when used alongside FPS Tools.
- For third-person camera control, use [FPS Tools](fps-tools.md) with `Perspective=Third Person`.
