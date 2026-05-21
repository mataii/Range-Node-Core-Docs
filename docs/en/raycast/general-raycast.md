# General Raycast

**Type:** Action (branch)  
**Category:** RAYCAST

Casts a ray between two arbitrary world-space points. More flexible than [Raycast](raycast.md) because the origin and destination can be defined via sockets.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Ray ID | String | `"grc1"` | Shared identifier with Raycast Result |
| Distance | Float | `10.0` | Maximum length (used when To socket is unlinked) |
| Prop Filter | String | `""` | Only hit objects with this BGE property |
| X-Ray | Bool | `False` | Pass through objects that don't match Prop Filter |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| From X / From Y / From Z | Input | Data (Float) — ray origin (unlinked = object position) |
| To X / To Y / To Z | Input | Data (Float) — ray destination (unlinked = forward × Distance) |
| Hit | Output | Exec |
| Miss | Output | Exec |

## Generated code

```python
import mathutils as _mu
_grc_from = self.own.worldPosition.copy()
_grc_to   = _grc_from + (self.own.worldOrientation * _mu.Vector((0.0, -1.0, 0.0))).normalized() * 10.0
_grc_dist = 10.0
_grc_res  = self.own.rayCast(_grc_to, _grc_from, _grc_dist, '', 0, 0, 0)
self._rc_grc1_hit = _grc_res[0]
self._rc_grc1_pos = list(_grc_res[1]) if _grc_res[1] else [0.0, 0.0, 0.0]
self._rc_grc1_nrm = list(_grc_res[2]) if _grc_res[2] else [0.0, 0.0, 1.0]
if _grc_res[0] is not None:
    # Hit branch
else:
    # Miss branch
```

## Typical usage

### Ray between two scene objects

```
[On Update]
    → [General Raycast:
           From X={Object Position: obj=emitter, Axis=X}
           From Y={Object Position: obj=emitter, Axis=Y}
           From Z={Object Position: obj=emitter, Axis=Z}
           To X={Object Position: obj=target, Axis=X}
           To Y={Object Position: obj=target, Axis=Y}
           To Z={Object Position: obj=target, Axis=Z}
           Ray ID="line_of_sight"]
          └── Hit  ──► [...]  # something between emitter and target
          └── Miss ──► [...]  # clear line of sight
```

### Ray from ground upward (ceiling check)

```
[On Update]
    → [General Raycast:
           From X={…} From Y={…} From Z={…}
           To Z={Math: {Object Position: Axis=Z} + 2.0}
           Ray ID="ceiling"]
          └── Hit  ──► [Send Message: Subject="ceiling_hit"]
          └── Miss ──► [...]
```

## Notes

- If `From` sockets are unlinked, the origin is the object's position.
- If `To` sockets are unlinked, the destination is `From + forward × Distance`.
- Stores results in `self._rc_<ID>_hit/pos/nrm`, same as [Raycast](raycast.md).
