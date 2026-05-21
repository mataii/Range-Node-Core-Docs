# Align To Normal

**Type:** Action  
**Category:** FPS

Aligns the component's object to the surface normal of the last raycast hit (`_rc_norm`). Must run in the **Hit** branch of [FPS Raycast](fps-raycast.md).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Up Axis | Enum | `Z` | Object axis that will point in the normal direction: `Z`, `Y`, or `X` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
if '_rc_norm' in dir() and _rc_norm:
    self.own.worldOrientation = _rc_norm.to_track_quat('Z', 'Y').to_matrix()
```

## Typical usage

### Object that sticks to the aimed surface

```
[On Update]
    → [FPS Raycast: Source=Camera, Axis=Y, Distance=5.0, Prop Filter="sticky"]
          └── Hit  ──► [Align To Normal: Up Axis=Z]
          └── Miss ──► [...]
```

### Character on a sloped surface

```
[On Update]
    → [FPS Raycast: Source=Object, Axis=Z, Distance=1.5]  # downward ray
          └── Hit  ──► [Align To Normal: Up Axis=Z]
          └── Miss ──► [...]
```

## Notes

- Only works if `_rc_norm` exists in the current execution scope — use [FPS Raycast](fps-raycast.md) in the same frame.
- To align a spawned object (not the component carrier), use [Spawn At Hit](spawn-at-hit.md) with `Align to Normal=True`.
