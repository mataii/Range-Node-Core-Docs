# Mouse Button

**Type:** Branch (True / Otherwise)  
**Category:** MOUSE

Detects the state of a mouse button: activated (first frame), active (held), or released.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Button | Enum | `Left` | `Left` / `Right` / `Middle` |
| Mode | Enum | `activated` | `activated` (first frame) / `active` (held) / `released` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| True | Output | Exec (condition met) |
| Otherwise | Output | Exec |

## Generated code

```python
if Range.logic.mouse.events[Range.events.LEFTMOUSE] == Range.logic.KX_INPUT_JUST_ACTIVATED:
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

Event constants by button:

| Button | Constant |
|--------|----------|
| Left | `Range.events.LEFTMOUSE` |
| Right | `Range.events.RIGHTMOUSE` |
| Middle | `Range.events.MIDDLEMOUSE` |

State constants by mode:

| Mode | Constant |
|------|----------|
| activated | `KX_INPUT_JUST_ACTIVATED` |
| active | `KX_INPUT_ACTIVE` |
| released | `KX_INPUT_JUST_RELEASED` |

## Typical usage

### Fire on left click hold

```
[On Update] → [Mouse Button: Button=Left, Mode=active]
                  └── True ──► [Weapon Fire Executor]
```

### Interact with right click

```
[On Update] → [Mouse Button: Button=Right, Mode=activated]
                  └── True ──► [Interaction System]
```

### Confirm selection on release

```
[On Update] → [Mouse Button: Button=Left, Mode=released]
                  └── True ──► [BTCustomTask: confirm_selection()]
```

## Notes

- `activated` fires for exactly one frame when the button is pressed.
- `active` fires every frame while the button is held.
- `released` fires for exactly one frame when the button is released.
