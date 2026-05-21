# Set Light Color

**Type:** Action  
**Category:** LIGHT

Changes the RGB color of a scene light at runtime.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Light Object | String | `""` | Light object name. Empty = `self.own` |
| R | Float | `1.0` | Red channel [0.0 – 1.0] |
| G | Float | `1.0` | Green channel [0.0 – 1.0] |
| B | Float | `1.0` | Blue channel [0.0 – 1.0] |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| R | Input | Data (Float) |
| G | Input | Data (Float) |
| B | Input | Data (Float) |

## Generated code

```python
scene.objects['lamp_main'].color = [1.0, 1.0, 1.0]
```

## Typical usage

### Red alert light

```
[On State Enter: FSM ID="alarm", State="alert"]
    └── On Enter ──► [Set Light Color: Light Object="alarm_lamp", R=1.0, G=0.0, B=0.0]
```

### Ambient light by time of day

```
[On Update] → [BTCustomTask:
                   h = self._bt_bb.get('hour', 12)
                   r = 1.0 if h > 18 else 0.8
                   g = 0.5 if h > 18 else 0.9
                   b = 0.2 if h > 18 else 1.0
              ] → [Set Light Color: R={r}, G={g}, B={b}]
```

## Notes

- `color` on `KX_LightObject` is a `[R, G, B]` list of floats.
- Values are passed directly with no range clamping — keep values between 0.0 and 1.0.
