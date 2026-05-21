# Set Light Range

**Type:** Action  
**Category:** LIGHT

Changes the influence range (distance) of a scene light at runtime.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Light Object | String | `""` | Light object name. Empty = `self.own` |
| Range | Float | `10.0` | New influence distance in world units |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| Range | Input | Data (Float) |

## Generated code

```python
scene.objects['lamp_main'].distance = 10.0
```

## Typical usage

### Explosion briefly illuminating surroundings

```
[On State Enter: FSM ID="explosion", State="blast"]
    └── On Enter ──► [Set Light Energy: Light Object="blast_light", Energy=5.0]
                  ──► [Set Light Range: Light Object="blast_light", Range=20.0]

[On State Exit: FSM ID="explosion", State="blast"]
    └── On Exit ──► [Set Light Range: Light Object="blast_light", Range=0.0]
```

## Notes

- `distance` maps to `KX_LightObject.distance` in Range Game Engine.
- A value of `0.0` effectively removes all light influence.
- Attenuation type (linear, quadratic) is controlled by the light material in the editor, not by this node.
