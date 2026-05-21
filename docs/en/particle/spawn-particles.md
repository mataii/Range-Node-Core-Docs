# Spawn Particles

**Type:** Action  
**Category:** PARTICLE

Instances a particle emitter into the scene using `scene.addObject()`. Ideal for one-shot effects like explosions, impacts, and splashes.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Emitter | String | `"fx_explosion"` | Name of the emitter object to instance (must be in an inactive layer) |
| Reference | String | `""` | Reference object for position/orientation. Empty = `self.own` |
| Lifetime | Int | `60` | Lifetime of the instanced object in frames (0 = infinite) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
scene.addObject('fx_explosion', self.own, 60)
```

With a custom reference object:

```python
scene.addObject('fx_explosion', scene.objects['hit_point'], 60)
```

## Typical usage

### Explosion on impact

```
[On Collision: Property="explosive"] → [Spawn Particles:
                                            Emitter = "fx_explosion"
                                            Lifetime = 120]
```

### Bullet hit effect

```
[Raycast: Hit] → [Spawn Particles:
                     Emitter = "fx_bullet_hit"
                     Reference = "hit_marker"
                     Lifetime = 30]
```

### Engine startup smoke

```
[On Start] → [Spawn Particles:
                 Emitter = "fx_engine_smoke"
                 Lifetime = 0]  # infinite
```

## Notes

- The `Emitter` object must exist in an inactive scene layer (not the active one), or the call will fail.
- `Lifetime = 0` creates the object with no time limit — it must be destroyed manually with `endObject()`.
- `scene.addObject()` returns the instanced object, but this node does not expose that value. Use [BT Custom Task](../ai-bt/bt-custom-task.md) if you need a reference to the spawned object.
