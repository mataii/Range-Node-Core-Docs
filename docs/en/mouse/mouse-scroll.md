# Mouse Scroll

**Type:** Branch (Up / Down / Otherwise)  
**Category:** MOUSE

Detects mouse wheel scrolling up or down.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Up | Output | Exec (scroll up) |
| Down | Output | Exec (scroll down) |
| Otherwise | Output | Exec (no scroll) |

## Generated code

```python
_ms_up   = Range.logic.mouse.events[Range.events.WHEELUPMOUSE]   == Range.logic.KX_INPUT_JUST_ACTIVATED
_ms_down = Range.logic.mouse.events[Range.events.WHEELDOWNMOUSE] == Range.logic.KX_INPUT_JUST_ACTIVATED
if _ms_up:
    #UP_PATH#
elif _ms_down:
    #DOWN_PATH#
else:
    #OTHERWISE_PATH#
```

## Typical usage

### Cycle inventory slots with scroll wheel

```
[On Update] → [Mouse Scroll]
                  ├── Up ──► [BTCustomTask: self.own['slot'] = max(0, self.own.get('slot',0)-1)]
                  └── Down ──► [BTCustomTask: self.own['slot'] = min(9, self.own.get('slot',0)+1)]
```

### Camera zoom with scroll wheel

```
[On Update] → [Mouse Scroll]
                  ├── Up ──► [BTCustomTask: cam.lens = min(80, cam.lens + 2)]
                  └── Down ──► [BTCustomTask: cam.lens = max(20, cam.lens - 2)]
```

## Notes

- `WHEELUPMOUSE` and `WHEELDOWNMOUSE` are single-frame events — they activate only on the frame the scroll occurs.
- There is no "held" state for the mouse wheel.
