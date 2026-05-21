# Object Position

**Type:** Data  
**Category:** MATH

Returns a component of a scene object's world position (`worldPosition`).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Object | String | `""` | Object name. Empty = `self.own` |
| Component | Enum | `X` | `X`, `Y`, `Z` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (Float) |

## Generated code

```python
# Component=X
scene.objects['waypoint'].worldPosition[0]

# self.own
self.own.worldPosition[1]  # Y
```

## Typical usage

### Compare heights of two objects

```
[On Update] → [BT Condition:
                  {Object Position: Object="Player", Component=Z} >
                  {Object Position: Component=Z}]
                  └── True ──► [...]  # player is above enemy
```

### Pass position to another system

```
[Play Tween:
    From X = {Object Position: Object="start_marker", Component=X}
    To X   = {Object Position: Object="end_marker",   Component=X}]
```

## Notes

- Data-only node — no Exec sockets.
- Returns `worldPosition[component_index]` directly — not transformed by object scale.
