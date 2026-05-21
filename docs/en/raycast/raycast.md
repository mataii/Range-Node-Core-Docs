# Raycast

**Type:** Action (branch)  
**Category:** RAYCAST

Casts a ray from the object in a direction and branches execution based on whether it hits something or not. Stores results by ID for [Raycast Result](raycast-result.md) to read.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Ray ID | String | `"ray1"` | Shared identifier with Raycast Result |
| Direction | Enum | `Forward (-Y)` | Ray direction: Forward, Backward, Up, Down, Right, Left, Custom, To Object |
| Target Object | Object | — | Only with `Direction = To Object` |
| Distance | Float | `10.0` | Maximum ray length |
| Prop Filter | String | `""` | Only hit objects with this BGE property (empty = all) |
| X-Ray | Bool | `False` | If True, pass through objects that don't match Prop Filter |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Dir X / Dir Y / Dir Z | Input | Data (Float) — only with Direction=Custom |
| Dist | Input | Data (Float) — overrides Distance property |
| Hit | Output | Exec |
| Miss | Output | Exec |

## Generated code

```python
import mathutils as _mu
_rc_dir  = (self.own.worldOrientation * _mu.Vector(( 0.0, -1.0,  0.0))).normalized()
_rc_orig = self.own.worldPosition.copy()
_rc_to   = _rc_orig + _rc_dir * float(10.0)
_rc_res  = self.own.rayCast(_rc_to, _rc_orig, float(10.0), '', 0, 0, 0)
self._rc_ray1_hit = _rc_res[0]
self._rc_ray1_pos = list(_rc_res[1]) if _rc_res[1] else [0.0, 0.0, 0.0]
self._rc_ray1_nrm = list(_rc_res[2]) if _rc_res[2] else [0.0, 0.0, 1.0]
if _rc_res[0] is not None:
    # Hit branch
else:
    # Miss branch
```

## Typical usage

### Detect obstacle ahead

```
[On Update] → [Raycast: Direction=Forward, Distance=2.0, Ray ID="obs"]
                  └── Hit  ──► [Send Message: Subject="obstacle_ahead"]
                  └── Miss ──► [...]
```

### Shoot toward an enemy

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [Raycast: Direction=To Object, Target=enemy, Distance=50.0, Prop Filter="enemy"]
          └── Hit  ──► [Send Message: Subject="take_damage", Body="10"]
          └── Miss ──► [...]
```

## Notes

- Results are stored in `self._rc_<ID>_hit`, `self._rc_<ID>_pos`, `self._rc_<ID>_nrm`.
- Use the same `Ray ID` in [Raycast Result](raycast-result.md) to read the values.
- With `Direction=Custom`, connect Dir X/Y/Z sockets to control the direction in local space.
- The ray is cast from the object's origin in world space.
