# Set Color

**Type:** Action  
**Category:** UI

Changes the RGBA color of an object (`obj.color`). Works with mesh and font objects.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Object | Enum | — | Object whose color to change |
| Color | Color (RGBA) | `(1, 1, 1, 1)` | Static color (used when no channel socket is connected) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| R | Input | Data (Float, 0.0 – 1.0) |
| G | Input | Data (Float, 0.0 – 1.0) |
| B | Input | Data (Float, 0.0 – 1.0) |
| A | Input | Data (Float, 0.0 – 1.0) |
| Out | Output | Exec |

## Generated code

```python
_col_obj = self.own.scene.objects.get('health_icon')
if _col_obj:
    _col_obj.color = [1.0, 0.2, 0.2, 1.0]
```

## Typical usage

### Damage flash (red on hit)

```
[On Message: Subject="hit"]
    → [Set Color: Object=player_mesh, R=1.0, G=0.0, B=0.0, A=1.0]
    → [Wait: Frames=10]
    → [Set Color: Object=player_mesh, R=1.0, G=1.0, B=1.0, A=1.0]
```

### Dynamic color based on heat

```
[On Update]
    → [Set Color:
           Object=heat_indicator
           R={Math: {Get Property: prop="heat"} / 100.0}
           G={Math: 1.0 - {Get Property: prop="heat"} / 100.0}
           B=0.0
           A=1.0]
```

### Transparency fade

```
[On Update]
    → [Set Color:
           Object=ghost_mesh
           R=1.0  G=1.0  B=1.0
           A={Tween Float: ID="fade_out"}]
```

## Notes

- If no R/G/B/A socket is connected, the node's static color picker is used.
- Values are expected in the `0.0 – 1.0` linear range.
- The object color (`obj.color`) multiplies the material color.
