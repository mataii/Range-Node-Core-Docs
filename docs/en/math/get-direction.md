# Get Direction

**Type:** Data  
**Category:** MATH

Returns a component (X, Y, or Z) of the normalized direction from the current object to a target object. Optionally flattens the vector to the XY plane.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| To | String | `"target"` | Target object |
| Component | Enum | `X` | Component to return: `X`, `Y`, `Z` |
| Flat | Bool | `False` | If True, ignores the Z difference before normalizing |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Component | Output | Data (Float) |

## Generated code

```python
# Component=X, Flat=False
_gdir = (scene.objects['target'].worldPosition - self.own.worldPosition).normalized()
_gdir[0]  # X component

# Flat=True: force Z=0 before normalizing
_gdir_raw = scene.objects['target'].worldPosition - self.own.worldPosition
_gdir_raw[2] = 0
_gdir = _gdir_raw.normalized()
_gdir[0]
```

## Typical usage

### Move AI toward player (X component)

```
[On Update] → [BTCustomTask:
                   dx = {Get Direction: To="Player", Component=X, Flat=True}
                   dy = {Get Direction: To="Player", Component=Y, Flat=True}
                   self.own.applyMovement([dx * speed, dy * speed, 0], False)]
```

## Notes

- The normalized direction has length 1.0 — returns components in the [-1.0, 1.0] range.
- With `Flat=True`, the vector has Z=0 and is normalized in XY — useful for ground-based AI that should not tilt.
- If the two objects are at the exact same position, `.normalized()` may return `Vector(0,0,0)`.
