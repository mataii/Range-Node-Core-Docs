# Ground Check

**Type:** Action (branch)  
**Category:** RAYCAST

Casts a ray downward from the object to check whether it is resting on the ground. Designed for character controllers and platformers.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Max Distance | Float | `1.1` | How far down to cast the ray from the origin |
| Origin Offset Z | Float | `0.0` | Vertical shift of the ray start point |
| Prop Filter | String | `""` | Only detect objects with this BGE property |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Grounded | Output | Exec |
| Airborne | Output | Exec |

## Generated code

```python
_gc_orig    = self.own.worldPosition.copy()
_gc_orig.z += 0.0
_gc_to      = _gc_orig.copy()
_gc_to.z   -= 1.1
_gc_res     = self.own.rayCast(_gc_to, _gc_orig, 1.1, '', 0, 0, 0)
self._ground_hit  = _gc_res[0]
self._ground_pos  = list(_gc_res[1]) if _gc_res[1] else [0.0, 0.0, 0.0]
self._ground_nrm  = list(_gc_res[2]) if _gc_res[2] else [0.0, 0.0, 1.0]
self._ground_dist = ((_gc_res[1] - _gc_orig).length if _gc_res[1] else 1.1)
if _gc_res[0] is not None:
    # Grounded branch
else:
    # Airborne branch
```

## Typical usage

### Jump control (only jump when grounded)

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [Ground Check: Max Distance=1.1]
          └── Grounded ──► [Apply Force: Z=5.0, Local=False]
          └── Airborne ──► [...]  # ignore
```

### State animation (run / fall)

```
[On Update]
    → [Ground Check: Max Distance=1.2]
          └── Grounded ──► [Play Action: Action="run_anim"]
          └── Airborne ──► [Play Action: Action="fall_anim"]
```

### Read ground distance in a script

```python
# Available as self._ground_dist after Ground Check runs
dist_to_ground = self._ground_dist
```

## Notes

- Result available in `self._ground_hit`, `self._ground_pos`, `self._ground_nrm`, `self._ground_dist`.
- Adjust `Origin Offset Z` if the object pivot is not at its base (e.g. character with center pivot).
- For ground-tagged surfaces use `Prop Filter="ground"` and add that property to the object in the editor.
