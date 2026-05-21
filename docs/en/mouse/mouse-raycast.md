# Mouse Raycast

**Type:** Branch (Hit / Miss)  
**Category:** MOUSE

Casts a ray from the active camera through the cursor position. Exposes the hit object, impact position, and surface normal.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Ray ID | String | `"mouse"` | Identifier to access results later |
| Distance | Float | `100.0` | Maximum ray distance |
| Prop Filter | String | `""` | Only detect objects with this BGE property |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Hit | Output | Exec (impact detected) |
| Miss | Output | Exec (no impact) |
| Object | Output | Data (hit object) |
| Position | Output | Data (impact position Vector) |
| Normal | Output | Data (surface normal Vector) |

## Generated code

```python
_mrc_cam = scene.active_camera
_mrc_hit, _mrc_pos, _mrc_nrm = _mrc_cam.getScreenRay(
    Range.logic.mouse.position[0],
    Range.logic.mouse.position[1],
    100.0,
    ''
) or (None, None, None)
self._rc_mouse_hit = _mrc_hit
self._rc_mouse_pos = _mrc_pos
self._rc_mouse_nrm = _mrc_nrm
if _mrc_hit:
    #HIT_PATH#
else:
    #MISS_PATH#
```

## Typical usage

### Place object at click point

```
[On Update] → [Mouse Button: Button=Left, Mode=activated]
                  └── True ──► [Mouse Raycast: Ray ID="place"]
                                    └── Hit ──► [Add Object:
                                                     Object = "building"
                                                     Position = {Position socket}]
```

### Aim weapon toward cursor

```
[On Update] → [Mouse Raycast: Ray ID="aim"]
                  └── Hit ──► [BTCustomTask:
                                   target = {Position socket}
                                   # orient weapon toward target...]
```

## Notes

- Results are stored in `self._rc_<ray_id>_hit/pos/nrm` for access from [Raycast Result](../raycast/raycast-result.md) if needed later.
- `getScreenRay` returns a `(object, position, normal)` tuple or `(None, None, None)` on miss.
