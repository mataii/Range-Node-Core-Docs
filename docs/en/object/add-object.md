# Add Object

**Type:** Action  
**Category:** OBJECT

Instances an object into the scene using `scene.addObject()`. The object must exist in an inactive layer.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Object | String | `"object"` | Name of the object to instance |
| Reference | String | `""` | Reference object for position/orientation. Empty = `self.own` |
| Lifetime | Int | `0` | Lifetime in frames (0 = infinite) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
scene.addObject('enemy_01', self.own, 0)
```

## Typical usage

### Spawn enemy on trigger activation

```
[On Collision: Property="spawn_trigger"] → [Add Object: Object="enemy_01", Lifetime=0]
```

### Instance a projectile

```
[Weapon Fire Executor] → [Add Object:
                              Object = "bullet_prefab"
                              Reference = "gun_muzzle"
                              Lifetime = 180]
```

## Notes

- The instanced object inherits the position and orientation of the reference object.
- `scene.addObject()` returns the instanced object, but this node does not expose that value. Use `BTCustomTask` if you need the reference.
- The source object must be in an inactive scene layer (not the active one).
