# Mouse Over

**Type:** Branch (Over / None)  
**Category:** MOUSE

Detects whether the mouse cursor is over a 3D object using a ray from the active camera. Allows filtering by object name or BGE property.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Target Name | String | `""` | Specific object name. Empty = any object |
| Prop Filter | String | `""` | Only detect objects with this BGE property. Empty = no filter |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Over | Output | Exec (cursor over object) |
| None | Output | Exec (no object under cursor) |

## Generated code

```python
_mo_cam = scene.active_camera
_mo_hit = _mo_cam.getScreenRay(
    Range.logic.mouse.position[0],
    Range.logic.mouse.position[1],
    100.0,
    ''  # prop_filter
)
if _mo_hit:
    #OVER_PATH#
else:
    #NONE_PATH#
```

With `target_name`:

```python
if _mo_hit and _mo_hit.name == 'target_name':
    #OVER_PATH#
```

## Typical usage

### Highlight interactable objects

```
[On Update] → [Mouse Over: Prop Filter="clickable"]
                  ├── Over ──► [BTCustomTask:
                                    # highlight object under cursor
                                    self._bt_bb['hovered'] = _mo_hit.name]
                  └── None ──► [BTCustomTask:
                                    self._bt_bb['hovered'] = None]
```

## Notes

- The hit object is available as `_mo_hit` in the node context.
- Similar to [Cursor Over](../cursor/cursor-over.md) — uses the same `getScreenRay` mechanism.
