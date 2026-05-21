# Gamepad Button

**Type:** Branch (Pressed / Released / Held / Otherwise)  
**Category:** GAMEPAD

Detects the state of a gamepad button: pressed (first frame), held (every frame while active), or released (first frame when released).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Gamepad Index | Int | `0` | Device slot (0–7) |
| Button Index | Int | `0` | Button index according to the OS driver |
| Mode | Enum | `HELD` | `PRESSED` / `HELD` / `RELEASED` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| True | Output | Exec (condition met) |
| Otherwise | Output | Exec (condition not met) |

## Generated code

```python
_gp = Range.logic.joysticks[0]
if _gp is not None:
    _gp_btn = 0 in _gp.activeButtons
    # HELD:
    if _gp_btn:
        #TRUE_PATH#
    else:
        #FALSE_PATH#
```

For `PRESSED` and `RELEASED`, uses a previous-frame flag in `globalDict`:

```python
_gp = Range.logic.joysticks[0]
if _gp is not None:
    _gp_btn  = 0 in _gp.activeButtons
    _gp_prev = Range.logic.globalDict.get('_gp0_prev_0', False)
    Range.logic.globalDict['_gp0_prev_0'] = _gp_btn
    # PRESSED: _gp_btn and not _gp_prev
    # RELEASED: not _gp_btn and _gp_prev
```

## Typical usage

### Jump with A button (Xbox)

```
[On Update] → [Gamepad Button: Index=0, Button=0, Mode=PRESSED]
                  └── True ──► [Player Jump]
```

### Fire while holding trigger

```
[On Update] → [Gamepad Button: Index=0, Button=7, Mode=HELD]
                  └── True ──► [Weapon Fire Executor]
```

### Reload on button release

```
[On Update] → [Gamepad Button: Index=0, Button=2, Mode=RELEASED]
                  └── True ──► [Weapon Reload]
```

## Notes

- Button indices vary by controller model and OS driver. Check them with `Print: {_gp.activeButtons}` during development.
- If the gamepad is not connected (`_gp is None`), the node falls to `Otherwise` without error.
