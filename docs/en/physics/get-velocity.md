# Get Velocity

**Type:** Data  
**Category:** PHYSICS

Returns a component of the object's linear velocity. Can return individual X, Y, Z components or magnitude speed on the XY plane or full XYZ.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Component | Enum | `X` | `X`, `Y`, `Z`, `Speed XY` (horizontal magnitude), `Speed XYZ` (3D magnitude) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (Float) |

## Generated code

```python
# Component = X
self.own.linearVelocity[0]

# Component = Speed XY
(_v := self.own.linearVelocity, (_v[0]**2 + _v[1]**2)**0.5)[-1]

# Component = Speed XYZ
self.own.linearVelocity.length
```

## Typical usage

### Display speedometer

```
[On Update] → [Set Text:
                  Text = {Number To String:
                              Value = {Get Velocity: Component=Speed XY}
                              Suffix = " m/s"
                              Decimals = 1}]
```

### Detect if object is moving

```
[On Update] → [BT Condition: {Get Velocity: Component=Speed XYZ} > 0.5]
                  └── True ──► [Play Action: Action="run_anim"]
```

### Check jump by vertical velocity

```
[On Update] → [BT Condition: {Get Velocity: Component=Z} > -0.1]
                  └── True ──► [...]  # on ground or rising
```

## Notes

- `linearVelocity` is the velocity vector in world units per second.
- `Speed XY` ignores the vertical component — useful for horizontal movement systems.
