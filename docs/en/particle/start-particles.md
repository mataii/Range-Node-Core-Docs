# Start Particles

**Type:** Action  
**Category:** PARTICLE

Activates an existing scene particle emitter: makes it visible and restarts the simulation with `reinstancePhysicsMesh()`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Emitter Object | String | `""` | Emitter object name in the scene. Empty = `self.own` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
_pe = scene.objects['fx_smoke']
_pe.setVisible(True)
_pe.reinstancePhysicsMesh()
```

## Typical usage

### Activate smoke on entering a damage zone

```
[On Collision: Property="damage_zone"] → [Start Particles: Emitter Object="fx_smoke"]
```

### Start particles with FSM

```
[On State Enter: FSM ID="engine", State="running"]
    └── On Enter ──► [Start Particles: Emitter Object="exhaust_fx"]
```

## Notes

- `reinstancePhysicsMesh()` resets the simulation from frame 0, producing fresh particles.
- If the emitter was already visible and active, this call restarts the particle animation.
- For a one-shot effect without needing to reset, use [Spawn Particles](spawn-particles.md).
