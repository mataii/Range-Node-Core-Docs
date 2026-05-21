# Cursor Over

**Type:** Branch (Over / None)  
**Category:** CURSOR

Detects whether the mouse cursor is over a 3D object using a ray from the active camera. Equivalent to a raycast through the cursor position.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Target Object | String | `""` | Specific object name to detect. Empty = any object |
| Prop Filter | String | `""` | Only detect objects with this BGE property. Empty = no filter |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Over | Output | Exec (cursor over target) |
| None | Output | Exec (cursor not over target) |

## Generated code

```python
_co_cam = scene.active_camera
_co_ray = _co_cam.getScreenRay(
    Range.logic.mouse.position[0],
    Range.logic.mouse.position[1],
    100.0,
    'interactable'  # prop_filter, or "" for no filter
)
if _co_ray and (_co_ray.name == 'button_ok' or not 'button_ok'):
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

## Typical usage

### Highlight object on hover

```
[On Update] → [Cursor Over: Prop Filter="interactable"]
                  ├── Over ──► [BTCustomTask: scene.objects[_co_ray.name].color = [1.5,1.5,1.5,1]]
                  └── None ──► [BTCustomTask: ...]  # restore colors
```

### Activate object on click

```
[On Update] → [Cursor Over: Target Object="button_start"]
                  └── Over ──► [Mouse Button: Button=Left, Mode=PRESSED]
                                    └── True ──► [Scene Switch: Scene="Game"]
```

## Notes

- Uses `getScreenRay()` from the active camera — the object must be in line of sight from the camera.
- If both `Target Object` and `Prop Filter` are empty, detects any object with geometry.
- The hit object is available as `_co_ray` in the node context (for custom code in `BT Custom Task`).
