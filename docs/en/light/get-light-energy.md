# Get Light Energy

**Type:** Data  
**Category:** LIGHT

Returns the current intensity of a scene light as a float value.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Light Object | String | `""` | Light object name. Empty = `self.own` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Energy | Output | Data (Float) |

## Generated code

```python
scene.objects['lamp_main'].energy
```

Pure expression inserted wherever the socket is connected.

## Typical usage

### Condition on current energy

```
[On Update] → [BT Condition: {Get Light Energy: Light Object="lamp"} > 0.5]
                  └── True ──► [...]
```

### Proportional adjustment

```
[On Update] → [BTCustomTask:
                   current = {Get Light Energy: Light Object="lamp_a"}
                   # reduce to 50% of current energy
              ] → [Set Light Energy: Light Object="lamp_b", Energy={current * 0.5}]
```

## Notes

- Data-only node — no Exec sockets.
- Reads `KX_LightObject.energy` directly each time it is evaluated.
