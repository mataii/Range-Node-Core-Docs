# Look At

**Type:** Action  
**Category:** OBJECT

Gradually rotates the object to face a target using spherical interpolation (slerp). Supports locking the Z axis for flat horizontal-only rotation.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Target | String | `"target"` | Name of the target object |
| Track Axis | Enum | `Y` | Object axis that points toward target: `X`, `Y`, `Z`, `-X`, `-Y`, `-Z` |
| Up Axis | Enum | `Z` | Axis that stays "up": `X`, `Y`, `Z` |
| Speed | Float | `0.1` | Interpolation factor [0.0 = no movement, 1.0 = instant] |
| Lock Z | Bool | `False` | If True, ignores height difference (flat rotation) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
_lat = scene.objects['target']
_la_dir = _lat.worldPosition - self.own.worldPosition
if True:  # lock_z
    _la_dir[2] = 0.0
if _la_dir.length > 0.001:
    _la_rot = _la_dir.to_track_quat('Y', 'Z')
    self.own.worldOrientation = self.own.worldOrientation.slerp(_la_rot.to_matrix(), 0.1)
```

## Typical usage

### Turret tracking player

```
[On Update] → [Look At: Target="Player", Track Axis=Y, Speed=0.05, Lock Z=True]
```

### Enemy looking at player in 3D

```
[On Update] → [On State: FSM ID="ai", State="aim"]
                  └── True ──► [Look At: Target="Player", Speed=0.15]
```

### UI arrow pointing at objective

```
[On Update] → [Look At: Target="objective_marker", Track Axis=-Y, Speed=1.0]
```

## Notes

- `Lock Z=True` is useful for characters and ground turrets that should not tilt when following objects at different heights.
- With `Speed=1.0`, rotation is instantaneous (no interpolation).
- If the target is at the exact same position, the function returns without doing anything (zero-vector guard).
