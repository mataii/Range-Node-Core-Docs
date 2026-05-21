# Gamepad Connected

**Type:** Branch (Connected / Not Connected)  
**Category:** GAMEPAD

Checks whether a gamepad is connected at the specified slot. Useful for adapting controls or displaying "connect a controller" messages.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Gamepad Index | Int | `0` | Device slot (0–7) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Connected | Output | Exec (gamepad connected) |
| Not Connected | Output | Exec (no gamepad) |

## Generated code

```python
if Range.logic.joysticks[0] is not None:
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

## Typical usage

### Switch between keyboard and gamepad controls

```
[On Update] → [Gamepad Connected: Index=0]
                  ├── Connected ──► [Gamepad Axis: ...]
                  └── Not Connected ──► [Player Input: ...]
```

### Show warning if no controller

```
[On Start] → [Gamepad Connected: Index=0]
                 └── Not Connected ──► [Node Broadcast: Subject="show_controller_warning"]
```

## Notes

- The check is O(1) — it only tests whether the slot is `None`.
- If the user disconnects the controller during gameplay, the slot may become `None` on any frame.
