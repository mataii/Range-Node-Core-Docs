# Set Damping

**Type:** Action  
**Category:** PHYSICS

Sets the linear and angular damping of the object, controlling how quickly it decelerates due to resistance.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Linear | Float | `0.04` | Linear damping (0 = no friction, 1 = instant stop) |
| Angular | Float | `0.1` | Angular damping (rotation) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own.setDamping(0.04, 0.1)
```

## Typical usage

### Set character friction

```
[On Start] → [Set Damping: Linear=0.8, Angular=0.9]
```

### Object in water (high resistance)

```
[On Collision: Object="water"]
    → [Set Damping: Linear=0.6, Angular=0.6]
```

### Object in air (low resistance)

```
[On Collision: Object="air_zone"]
    → [Set Damping: Linear=0.01, Angular=0.01]
```

## Notes

- Damping is applied each frame as a fraction of the current velocity.
- Values of `0.0` fully remove air-resistance braking.
- For slippery surfaces, use low `Linear` values combined with moderate movement forces.
