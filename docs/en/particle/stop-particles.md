# Stop Particles

**Type:** Action  
**Category:** PARTICLE

Deactivates a particle emitter by hiding it with `setVisible(False)`. Already-emitted particles may continue until they expire depending on the system settings.

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
scene.objects['fx_smoke'].setVisible(False)
```

## Typical usage

### Stop smoke when exiting a damage zone

```
[On State Exit: FSM ID="engine", State="running"]
    └── On Exit ──► [Stop Particles: Emitter Object="exhaust_fx"]
```

### Start / Stop pair

```
# On entering charging state:
[On State Enter: FSM ID="weapon", State="charging"]
    └── On Enter ──► [Start Particles: Emitter Object="charge_fx"]

# On leaving charging state:
[On State Exit: FSM ID="weapon", State="charging"]
    └── On Exit ──► [Stop Particles: Emitter Object="charge_fx"]
```

## Notes

- Hiding the emitter does not destroy in-flight particles — behavior depends on the particle system lifetime setting.
- To completely destroy an instanced emitter, use `endObject()` (available in [BT Custom Task](../ai-bt/bt-custom-task.md)).
