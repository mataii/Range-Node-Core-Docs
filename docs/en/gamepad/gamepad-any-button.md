# Gamepad Any Button

**Type:** Branch (Any / None)  
**Category:** GAMEPAD

Detects whether any button on the gamepad is being pressed in the current frame.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Gamepad Index | Int | `0` | Device slot (0–7) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Any | Output | Exec (at least one button active) |
| None | Output | Exec (no buttons active) |

## Generated code

```python
_gp = Range.logic.joysticks[0]
if _gp is not None and len(_gp.activeButtons) > 0:
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

## Typical usage

### Exit start screen on any button press

```
[On Update] → [Gamepad Any Button: Index=0]
                  └── Any ──► [Scene Switch: Scene="MainMenu"]
```

### Dynamically activate gamepad mode

```
[On Update] → [Gamepad Any Button: Index=0]
                  └── Any ──► [Set Property: prop="input_mode", Value="gamepad"]
```

## Notes

- `activeButtons` is a list of active button indices. `len() > 0` means at least one is active.
- Does not distinguish between pressed/held — detects any active button in the current frame. Use [Gamepad Button](gamepad-button.md) with `Mode=PRESSED` for first-frame-only detection.
