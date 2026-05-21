# Spawn At Hit

**Type:** Action  
**Category:** FPS

Spawns an object at the last raycast hit point (`_rc_pos`). Must run in the **Hit** branch of [FPS Raycast](fps-raycast.md) so that `_rc_pos` and `_rc_norm` are available.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Object | Enum | — | Object to spawn (must be inactive in the scene as a template) |
| Lifetime | Int | `120` | Duration in frames before the spawned object is removed (0 = permanent) |
| Align to Normal | Bool | `True` | If True, rotates the spawned object to align with the surface normal |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
_sah_spawned = self.own.scene.addObject('bullet_hole', self.own, 120)
if _sah_spawned: _sah_spawned.worldPosition = _rc_pos
if _rc_norm and _sah_spawned:
    _sah_spawned.worldOrientation = _rc_norm.to_track_quat('Z', 'Y').to_matrix()
```

## Typical usage

### Bullet impact decal

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [FPS Raycast: Source=Camera, Axis=Y, Distance=50.0]
          └── Hit  ──► [Spawn At Hit: Object="bullet_decal", Lifetime=300, Align to Normal=True]
          └── Miss ──► [...]
```

### Spawn impact particles

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [FPS Raycast: Source=Camera, Axis=Y, Distance=50.0]
          └── Hit  ──► [Spawn At Hit: Object="spark_particle", Lifetime=60, Align to Normal=False]
          └── Miss ──► [...]
```

## Notes

- Requires `_rc_pos` to exist in the same frame — connect directly in the Hit branch of `FPS Raycast`.
- The template object must be in the scene but hidden (inactive) to be spawnable via `addObject`.
- With `Align to Normal=True`, the spawned object's Z axis points in the surface normal direction.
