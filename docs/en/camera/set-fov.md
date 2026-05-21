# Set FOV

**Type:** Action  
**Category:** CAMERA

Changes the field of view of a camera by modifying the `lens` property of the camera object.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Camera | String | `""` | Camera object name. Empty = `scene.active_camera` |
| FOV | Float | `50.0` | Focal length in mm (lower = wider angle) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| FOV | Input | Data (Float) |

## Generated code

```python
scene.active_camera.lens = 50.0
```

With a specific object:

```python
scene.objects['scope_cam'].lens = 200.0
```

## Typical usage

### Zoom on aim (ADS)

```
[On Key Press: Key=RIGHTMOUSE, Mode=activated]
    → [Set FOV: FOV=25.0]  # zoom in

[On Key Press: Key=RIGHTMOUSE, Mode=released]
    → [Set FOV: FOV=50.0]  # normal FOV
```

### Dynamic FOV based on speed

```
[On Update] → [Set FOV:
                   FOV = {Math: A=50, B={Vehicle Speed: m_s * 0.5}, Op=ADD}]
```

## Notes

- `lens` is the focal length in mm, not a degree angle. Formula: `FOV_degrees = 2 * atan(sensor_size / (2 * lens))`.
- Typical values: 35mm = wide FOV, 50mm = normal, 100mm+ = telephoto/zoom.
- For smooth FOV animations, combine with [BT Custom Task](../ai-bt/bt-custom-task.md) interpolating the value each frame.
