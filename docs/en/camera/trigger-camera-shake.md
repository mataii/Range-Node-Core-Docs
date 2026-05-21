# Trigger Camera Shake

**Type:** Action  
**Category:** CAMERA

Triggers a camera shake effect by writing parameters to `globalDict`. Requires [Camera Shake](camera-shake.md) running on the camera object.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Intensity | Float | `0.3` | Maximum displacement amplitude |
| Frames | Int | `20` | Shake duration in frames |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
Range.logic.globalDict['_rnc_shk'] = {'intensity': 0.3, 'frames': 20, 'current': 0}
```

## Typical usage

### Shake on taking damage

```
[On Message: Subject="take_damage"] → [Trigger Camera Shake: Intensity=0.2, Frames=15]
```

### Explosion shake

```
[On Collision: Property="explosive"] → [Trigger Camera Shake: Intensity=0.8, Frames=40]
```

### Weapon recoil shake

```
[Weapon Fire Executor] → [Trigger Camera Shake: Intensity=0.1, Frames=5]
```

## Notes

- This node **only triggers** the shake. The camera object needs [Camera Shake](camera-shake.md) running every frame to apply the effect visually.
- If triggered while a shake is already active, parameters are overwritten with the new values.
