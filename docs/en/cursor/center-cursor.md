# Center Cursor

**Type:** Action  
**Category:** CURSOR

Moves the cursor to the screen center by setting `Range.logic.mouse.position = [0.5, 0.5]`.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
Range.logic.mouse.position = [0.5, 0.5]
```

## Typical usage

### Center cursor every frame for mouselook

```
[On Update] → [Center Cursor]
             → [Capture Mouse: Capture ID="cam"]
```

Centering the cursor each frame prevents it from reaching screen edges, which would clamp the delta.

### Center on entering FPS mode

```
[On State Enter: FSM ID="player", State="fps"]
    └── On Enter ──► [Set Cursor Visible: Mode=HIDE]
                  ──► [Center Cursor]
```

## Notes

- `mouse.position` accepts normalized values [0.0, 1.0] where [0.5, 0.5] is the exact center of the game window.
- Centering happens at the end of the frame it executes on — cursor movement that frame was already registered.
