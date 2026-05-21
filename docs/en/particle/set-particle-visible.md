# Set Particle Visible

**Type:** Action  
**Category:** PARTICLE

Shows, hides, or toggles the visibility of a particle emitter without restarting the simulation.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Emitter Object | String | `""` | Emitter object name. Empty = `self.own` |
| Mode | Enum | `TOGGLE` | `SHOW` = show, `HIDE` = hide, `TOGGLE` = switch |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
# SHOW
scene.objects['fx_smoke'].setVisible(True)

# HIDE
scene.objects['fx_smoke'].setVisible(False)

# TOGGLE
_spv = scene.objects['fx_smoke']
_spv.setVisible(not _spv.visible)
```

## Difference from Start / Stop Particles

| Set Particle Visible | Start Particles |
|---------------------|-----------------|
| Visibility change only | Visibility change + restart simulation |
| Simulation keeps running in the background | Resets from frame 0 |
| Lighter weight | More complete for reactivating an effect |

## Notes

- Using `HIDE` does not stop internal simulation computation — the emitter keeps advancing while invisible.
- For a clean pause-and-restart, use [Stop Particles](stop-particles.md) + [Start Particles](start-particles.md).
