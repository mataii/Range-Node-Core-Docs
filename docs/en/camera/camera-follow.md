# Camera Follow

**Type:** Action  
**Category:** CAMERA

Smoothly moves the camera toward a target object using linear interpolation (lerp) for position and, optionally, slerp for orientation.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Target | String | `"Player"` | Name of the object to follow |
| Offset | Vector | `[0, -5, 2]` | Offset from target in world space |
| Position Speed | Float | `0.1` | Lerp factor for position [0.0–1.0] |
| Rotation Speed | Float | `0.0` | Slerp factor for rotation. 0.0 = no automatic rotation |
| Camera | String | `""` | Camera to move. Empty = `scene.active_camera` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code (simplified)

```python
_cf_target = scene.objects['Player']
_cf_target_pos = _cf_target.worldPosition + Vector([0, -5, 2])
_cf_cam = scene.active_camera
_cf_cam.worldPosition = _cf_cam.worldPosition.lerp(_cf_target_pos, 0.1)
# If rotation_speed > 0:
_cf_cam.worldOrientation = _cf_cam.worldOrientation.slerp(_cf_target.worldOrientation, 0.0)
```

## Typical usage

### Third-person follow camera

```
[On Update] → [Camera Follow:
                   Target = "Player"
                   Offset = [0, -6, 3]
                   Position Speed = 0.08]
```

### Cinematic camera that follows and looks at character

```
[On Update] → [Camera Follow:
                   Target = "Player"
                   Offset = [3, -4, 2]
                   Position Speed = 0.05
                   Rotation Speed = 0.05]
```

## Notes

- `Offset` is applied in world space — it does not rotate with the target. For an offset relative to the target's forward, use `BTCustomTask` with `worldOrientation * Vector(offset)`.
- With `Position Speed=1.0`, the camera teleports instantly to the target.
- For third-person with player-controlled camera rotation, use [FPS Tools](../fps/fps-tools.md) or the [Mouse Look](../fps/mouse-look.md) system.
