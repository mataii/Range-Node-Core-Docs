# Camera Shake

**Type:** Processor (run on the camera)  
**Category:** CAMERA

Applies the camera shake effect each frame. Reads parameters from `globalDict` (written by [Trigger Camera Shake](trigger-camera-shake.md)) and applies random offsets with fade to the camera object.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code (simplified)

```python
_shk = Range.logic.globalDict.get('_rnc_shk', None)
if _shk and _shk['current'] < _shk['frames']:
    _shk['current'] += 1
    _shk_t = 1.0 - (_shk['current'] / _shk['frames'])  # fade 1→0
    _shk_i = _shk['intensity'] * _shk_t
    import random
    _shk_off = [random.uniform(-_shk_i, _shk_i) for _ in range(3)]
    _shk_off[2] = 0  # X and Y only
    self.own.applyMovement(_shk_off, True)
```

## Where to place this node

Must run on the **camera object**, not the object that triggers the shake:

```
# Camera object's node tree:
[On Update] → [Camera Shake]
```

## Typical usage

### Complete camera shake setup

```
# "Player" object — triggers the shake:
[On Message: Subject="take_damage"]
    → [Trigger Camera Shake: Intensity=0.3, Frames=20]

# "Camera" object — processes the shake every frame:
[On Update] → [Camera Shake]
```

## Notes

- Fade is linear: maximum displacement on the first frame, reduces to 0 at the end.
- Displacement is applied in camera local space (`applyMovement(..., True)`).
- If `globalDict['_rnc_shk']` doesn't exist or the shake has ended, the node does nothing.
