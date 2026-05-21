# Toggle Light

**Type:** Action  
**Category:** LIGHT

Turns a scene light on, off, or toggles it. Uses `setVisible()` on the light object.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Light Object | String | `""` | Light object name. Empty = `self.own` |
| Mode | Enum | `TOGGLE` | `ON` = enable, `OFF` = disable, `TOGGLE` = switch |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
# Mode = ON
scene.objects['lamp_main'].setVisible(True)

# Mode = OFF
scene.objects['lamp_main'].setVisible(False)

# Mode = TOGGLE
_tl_obj = scene.objects['lamp_main']
_tl_obj.setVisible(not _tl_obj.visible)
```

## Typical usage

### Room light when entering

```
[On Collision: Object="player"] → [Toggle Light: Light Object="room_lamp", Mode=ON]
```

### Light flicker with timer

```
[On Timer: Duration=0.1, Repeat=True] → [Toggle Light: Light Object="flicker_lamp", Mode=TOGGLE]
```

### Turn off all lights on death

```
[On Message: Subject="player_died"] → [Toggle Light: Light Object="lamp_1", Mode=OFF]
                                    → [Toggle Light: Light Object="lamp_2", Mode=OFF]
```

## Notes

- `setVisible(False)` hides the object and its light emission. The light stops illuminating the scene.
- `TOGGLE` mode reads the object's `.visible` property at execution time — may flicker if called multiple times per frame.
