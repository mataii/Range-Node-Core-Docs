# Get Distance

**Type:** Data  
**Category:** MATH

Returns the distance between two scene objects in world units. Can measure in XY (flat 2D) or XYZ (full 3D).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| From | String | `""` | Source object. Empty = `self.own` |
| To | String | `"target"` | Target object |
| Mode | Enum | `XYZ` | `XY` = flat 2D distance, `XYZ` = full 3D distance |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Distance | Output | Data (Float) |

## Generated code

```python
# XYZ
(self.own.worldPosition - scene.objects['target'].worldPosition).length

# XY (ignores Z)
_gd_a = self.own.worldPosition
_gd_b = scene.objects['target'].worldPosition
((_gd_a[0]-_gd_b[0])**2 + (_gd_a[1]-_gd_b[1])**2)**0.5
```

## Typical usage

### Detect player proximity

```
[On Update] → [BT Condition: {Get Distance: To="Player"} < 5.0]
                  └── True ──► [Set State: State="chase"]
```

### Horizontal distance for ground-based AI

```
[On Update] → [BT Condition: {Get Distance: To="Player", Mode=XY} < 3.0]
                  └── True ──► [Set State: State="attack"]
```

## Notes

- Uses `worldPosition` — ignores object scale. Measures distance between object centers.
- `XY` mode is useful for ground-based AI where height difference should not affect detection.
