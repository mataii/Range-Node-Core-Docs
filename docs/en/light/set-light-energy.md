# Set Light Energy

**Type:** Action  
**Category:** LIGHT

Changes the intensity of a scene light at runtime.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Light Object | String | `""` | Light object name. Empty = `self.own` |
| Energy | Float | `1.0` | New intensity value |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| Energy | Input | Data (Float) |

## Generated code

```python
scene.objects['lamp_main'].energy = 1.0
```

## Typical usage

### Gradually brighten a light

```
[On Update] → [BTCustomTask:
                   t = min(1.0, {Get State Time: FSM ID="light"} / 2.0)
              ] → [Set Light Energy: Light Object="lamp", Energy={t}]
```

### Light pulse (soft flicker effect)

```
[On Update] → [Set Light Energy:
                   Light Object = "lamp_flicker"
                   Energy = {Math: sin(getClockTime() * 5) * 0.5 + 1.0}]
```

## Notes

- `energy` maps to `KX_LightObject.energy` in Range Game Engine.
- Negative values are allowed by the API but produce undefined visual results.
