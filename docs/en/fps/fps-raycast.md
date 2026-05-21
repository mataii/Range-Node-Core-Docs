# FPS Raycast

**Type:** Action (branch)  
**Category:** FPS

Casts a ray from the active camera (or the object itself) along the configured axis. Directly exposes hit object, position, and normal output sockets — especially convenient for shooting and selection mechanics in FPS games.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Source | Enum | `Camera` | Ray origin: `Camera` (active FPS camera) or `Object` (the object itself) |
| Axis | Enum | `Forward (Y)` | Shoot axis in local space: `Y` (forward), `X` (right), `Z` (up) |
| Distance | Float | `10.0` | Maximum ray length |
| Property Filter | String | `""` | Only hit objects with this BGE property |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Hit | Output | Exec |
| Miss | Output | Exec |
| Object | Output | Data (KX_GameObject or None) |
| Position | Output | Data (Vector) |
| Normal | Output | Data (Vector) |

## Generated code

```python
_ray_src = (self._fps_cam if (hasattr(self, '_fps_cam') and self._fps_cam) else self.own)
_ray_to = _ray_src.worldPosition + _ray_src.worldOrientation * mathutils.Vector((0, 10.0, 0))
_rc_obj, _rc_pos, _rc_norm = self.own.rayCast(_ray_to, _ray_src.worldPosition, 10.0, '', 1, 1)
if _rc_obj:
    # Hit branch
else:
    # Miss branch
```

## Typical usage

### Shooting with damage

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [FPS Raycast: Source=Camera, Axis=Y, Distance=50.0, Prop Filter="enemy"]
          └── Hit  ──► [BTCustomTask: _rc_obj['health'] = _rc_obj.get('health', 100) - 25]
          └── Miss ──► [...]
```

### Show name of aimed object

```
[On Update]
    → [FPS Raycast: Source=Camera, Axis=Y, Distance=5.0]
          └── Hit  ──► [Set Text: Object=crosshair_label, Text=_rc_obj.name]
          └── Miss ──► [Set Text: Object=crosshair_label, Text=""]
```

## Notes

- `_rc_obj`, `_rc_pos`, and `_rc_norm` are accessible in downstream nodes within the same frame.
- With `Source=Camera`, the node looks for `self._fps_cam` — set by [FPS Tools](fps-tools.md) or [Mouse Look](mouse-look.md).
- Different from `Raycast` in the RAYCAST category: no Ray ID is used; results are emitted directly as execution-scope variables.
