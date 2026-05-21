# Apply Force

**Type:** Action  
**Category:** PHYSICS

Applies an instantaneous force to the object using `applyForce()`. Force can be applied in local or world space.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| X | Float | `0.0` | X force component |
| Y | Float | `0.0` | Y force component |
| Z | Float | `0.0` | Z force component |
| Local | Bool | `False` | If True, applies in object local space |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| X, Y, Z | Input | Data (Float) |

## Generated code

```python
self.own.applyForce([0.0, 0.0, 100.0], False)
```

## Typical usage

### Jump impulse

```
[On Key Press: Key=SPACE, Mode=PRESSED] → [Apply Force: Z=500.0, Local=False]
```

### Radial explosion (BTCustomTask)

```
[On Collision: Property="explosive"] → [BTCustomTask:
                                            dir = self.own.worldPosition - explosion_pos
                                            dir.normalize()
                                            self.own.applyForce(dir * 800, False)]
```

## Notes

- Force is applied per frame if called from `On Update`. For a single impulse, call from `On Key Press` with mode `PRESSED` or from `On Start`.
- The object's mass affects the resulting acceleration (F = ma).
